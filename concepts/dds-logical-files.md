---
title: DDS 逻辑文件
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [dds, ibm-i, database]
sources: [raw/papers/dds-physical-logical-7.5.txt]
---

# DDS 逻辑文件

逻辑文件（`*LF`）是 DB2 for i 的视图/索引层，本身不存储数据，而是定义如何访问物理文件中的数据。

## 三种逻辑文件类型

| 类型 | 说明 | 记录格式数 | 物理文件数 |
|------|------|-----------|-----------|
| **简单逻辑文件** | 单记录格式，单物理文件 | 1 | 1 |
| **多格式逻辑文件** | 多记录格式，多物理文件 | ≥1 或 ≥1 PF | ≥1 |
| **Join 逻辑文件** | 跨物理文件关联字段 | 1 | 2-256 |

## 简单逻辑文件

```dds
*  简单逻辑文件: 按客户号+日期索引
              R CUSTORD                   PFILE(CUSTOMER/ORDERS)
              K CUSTID                     主键: 客户号
              K ORDDATE                     次键: 订单日期
              S STATUS ('A')               只选 STATUS='A' 的记录
```

### Select/Omit 条件

```dds
              R CUSTORD                   PFILE(CUSTOMER/ORDERS)
              K CUSTID
              K ORDDATE
              S STATUS ('A' 'D')          Select: STATUS = A 或 D
              O STATUS ('X')              Omit: STATUS = X
```

- `S` (Select)：只读符合条件的记录
- `O` (Omit)：排除符合条件的记录
- `AND` 隐含，多条件时连续写

### DYNSLT（动态选择）

默认 Select/Omit 在打开文件时评估（静态）。加 `DYNSLT` 可在每次读时动态评估：

```dds
              R CUSTORD                   PFILE(CUSTOMER/ORDERS)
                                          DYNSLT
              K CUSTID
              S AMOUNT (1000)
```

## 多格式逻辑文件

多格式逻辑文件用 `PFILE` 指向多个物理文件，或同一物理文件有多个记录格式：

```dds
*  多格式逻辑文件: 同时覆盖 CUSTOMER 和 SUPPLIER 两个物理文件
              R CUSTREC                   PFILE(CUSTOMER SUPPLIER)
              K CUSTID
              NAME                         字段名（两文件公共字段）
              BALANCE

              R SUPPREC                   PFILE(CUSTOMER SUPPLIER)
              K SUPPLIERID
              SUPPLIERNAME
              CONTACT
```

> 各记录格式只使用各物理文件中的**公共字段**。

## Join 逻辑文件

Join 逻辑文件将不同物理文件的字段合并为单一记录格式，类似 SQL JOIN：

```dds
*  Join 逻辑文件: 合并客户信息和地址信息
              R CUSTADDR                 JFILE(CUSTOMER ADDRFILE)
              J                           JOIN(CUSTOMER ADDRFILE)
              JFLD(CUSTID CUSTID)         Join 字段: CUSTOMER.CUSTID = ADDRFILE.CUSTID
              JREF(1)                      字段引用文件 1 (CUSTOMER)
              JREF(2)                      字段引用文件 2 (ADDRFILE)
              CUSTID                       来自 CUSTOMER（隐含 JREF(1)）
              CUSTNAME                     来自 CUSTOMER
              ADDR
              PHONE
```

### Join 关键字

| 关键字 | 说明 |
|--------|------|
| `JFILE(file1 file2...)` | 指定参与 Join 的物理文件（最多 256 个）|
| `JOIN(file1 file2)` | 指定哪对文件参与当前 Join 规范 |
| `JFLD(field1 field2)` | 指定 Join 等值条件 |
| `JREF(n)` | 字段引用第 n 个物理文件 |
| `JDFTVAL` | Join 时右表无匹配也返回左表记录（类似 LEFT JOIN） |
| `JDUPSEQ(field)` | 重复键值时的排序顺序 |

### JDFTVAL 示例

```
CUSTOMER (左表):          ADDRFILE (右表):
CUSTID  NAME              CUSTID  CITY
C001    Alice             C001    Shanghai
C002    Bob               C003    Beijing

使用 JDFTVAL:
结果: Alice+Shanghai, Bob+(NULL), (C003 无匹配，不出现)

不使用 JDFTVAL:
结果: Alice+Shanghai (C002 Bob 被丢弃)
```

### 多表 Join

```dds
              R COMBINED                  JFILE(PF1 PF2 PF3)
              J                           JOIN(1 2)    PF1 JOIN PF2
              JFLD(ID1 ID2)
              J                           JOIN(2 3)    PF2 JOIN PF3
              JFLD(ID2 ID3)
              JREF(1)
              FLDA
              JREF(2)
              FLDB
              JREF(3)
              FLDC
```

## 键字段与访问路径

```dds
*  访问路径类型:
*  1. 无键 → 到达顺序（Arrival Sequence）
*  2. K 键 → 键控顺序（Keyed Sequence），唯一时加 UNIQUE
*  3. REFACCPTH → 复制另一文件的访问路径

              R ORDERREC                   PFILE(MYLIB/ORDERS)
              K ORDDATE                    升序
              K CUSTID                     次序键
              K ORDAMT                     金额（同一日期/客户内按金额排序）
```

### 降序键

```dds
              K ORDDATE                    升序（默认）
              K CUSTID
              K ORDAMT *DESCEND           金额降序
```

## 字段映射关键字

### RENAME（重命名字段）

```dds
              R REPORTLF                   PFILE(CUSTOMER/ORDERS)
              ORDNBR                       沿用原名
              RENAME(ORDAMT:SALEAMT)       重命名输出字段
```

### CONCAT（字段拼接）

```dds
              R COMBINED                   PFILE(PERSON)
              FULLNAME                     字符字段 = FIRSTNAME + LASTNAME
              CONCAT(FIRSTNAME LASTNAME)
```

### SST（子字符串）

```dds
              R PARTIAL                    PFILE(CODEFILE)
              PARTABBR   5A               取 CODE 字段的 1-5 位
              SST(CODE 1 5)
              PARTYPE    2A               取 CODE 字段的 6-7 位
              SST(CODE 6 2)
```

## ALIAS（字段别名）

```dds
              R ORDFMT
              ORDNBR       10A             ALIAS('Order-Number')
              CUSTID        7P 0           ALIAS('Customer-ID')
```

## 创建逻辑文件

```bash
# 从 DDS 创建逻辑文件
CRTLF FILE(MYLIB/CUSTORD) SRCFILE(MYLIB/QDDSSRC) SRCMBR(CUSTORD)

# 查看结构
DSPFFD FILE(MYLIB/CUSTORD)

# 查看访问路径
DSPPFM FILE(MYLIB/CUSTORD)
```

## 逻辑文件 vs SQL VIEW

| 特性 | 逻辑文件 | SQL VIEW |
|------|---------|---------|
| 数据来源 | DDS 定义 | SQL SELECT |
| Select/Omit | 支持 | WHERE 子句 |
| Join | JFLD (仅等值) | INNER/LEFT/RIGHT JOIN |
| 列拼接 | CONCAT | \|\| |
| 列截取 | SST | SUBSTR() |
| 索引 | 随文件创建 | 独立 CREATE INDEX |

## 相关页面

- [[dds-physical-files]] — 物理文件详解
- [[dds-keywords]] — DDS 关键字速查
- [[rpg-file-processing]] — RPG 文件处理
- [[ibm-i-platform]] — IBM i 平台
