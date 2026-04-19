---
title: DDS 关键字速查
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [dds, ibm-i, database]
sources: [raw/papers/dds-physical-logical-7.5.txt]
---

# DDS 关键字速查

按级别分类的 DDS 关键字完整参考。

## 文件级关键字（File-Level）

| 关键字 | 适用 | 说明 |
|--------|------|------|
| `TEXT('desc')` | PF/LF | 文件描述文本 |
| `REF(lib/file)` | PF | 引用另一文件的字段定义 |
| `UNIQUE` | PF | 键值必须唯一 |
| `LIFO` / `FIFO` / `FCFO` | PF | 溢出记录处理 |
| `ALTSEQ(*NONE)` | PF/LF | 替代排序序列 |
| `CCSID(n)` | PF/LF | 编码字符集 |
| `FRCATR(*NONE)` | PF | 不使用参考文件属性 |
| `REFSHIFT` | PF/LF | 字段名引用时含 Shift 状态 |
| `DATFMT(*ISO/*USA/*EUR/*JIS/*JOB)` | PF/LF | 日期格式 |
| `DATSEP(*SLASH/*DASH/*PERIOD/*JOB)` | PF/LF | 日期分隔符 |
| `DYNSLT` | LF | 动态 Select/Omit |
| `JFILE(file1 file2...)` | Join LF | 指定 Join 的物理文件 |
| `PFILE(lib/file)` | LF | 指定物理文件 |
| `REFACCPTH(lib/file)` | LF | 引用另一文件的访问路径 |
| `RENAME(newname)` | LF | 记录格式重命名 |

## 记录级关键字（Record-Level）

| 关键字 | 适用 | 说明 |
|--------|------|------|
| `FORMAT(lib/file fmt)` | LF | 引用其他文件的记录格式 |
| `JDFTVAL` | Join LF | 无匹配时返回左表默认值 |

## 字段级关键字（Field-Level）

### 标识与描述

| 关键字 | 说明 |
|--------|------|
| `TEXT('desc')` | 字段描述 |
| `COLHDG('Line1' 'Line2' 'Line3')` | 显示列标题 |
| `ALIAS('alias-name')` | 字段别名 |

### 数据定义

| 关键字 | 说明 |
|--------|------|
| `DFT(value)` | 默认值 |
| `REFFLD(lib/file/field)` | 引用字段属性 |
| `DATFMT(*ISO/*USA...)` | 日期格式 |
| `DATSEP(*SLASH...)` | 日期分隔符 |
| `ALWNULL` | 允许 NULL（仅 PF） |
| `VARLEN` | 可变长度 |
| `SIGNED` | 带符号数值 |
| `ABSVAL` | 绝对值（用于排序） |

### 校验

| 关键字 | 说明 |
|--------|------|
| `CHECK(AB/ME/MF/M10/M10F/M11/M11F/VN/VNE)` | 校验码检验 |
| `COMP(*EQ/*NE/*GT/*LT/*GE/*LE/*NG/*NL)` | 比较操作符 |
| `RANGE(low:high)` | 取值范围 |
| `VALUES('val1':'val2':'val3')` | 允许值列表 |
| `CHKMSGID(msgid pgm)` | 校验失败发送消息 |

### 编辑与显示

| 关键字 | 说明 |
|--------|------|
| `EDTCDE(A-K/W/X/Y/Z/1-4)` | 编辑码 |
| `EDTWRD('xxxx')` | 编辑字（自定义格式） |
| `DIGIT` | 纯数字字符字段 |

### 日期/时间/时间戳

| 关键字 | 说明 |
|--------|------|
| `DATFMT(*JOB/*ISO/*USA/*EUR/*JIS/*MDY/*DMY/*YMD)` | 日期格式 |
| `DATSEP(*JOB/*SLASH/*DASH/*PERIOD)` | 日期分隔符 |

## Join 关键字（Join-Level）

| 关键字 | 说明 |
|--------|------|
| `JOIN(file1 file2)` | 指定 Join 的文件对 |
| `JFLD(from to)` | 指定 Join 等值字段 |
| `JREF(n)` | 字段引用第 n 个文件 |
| `JDUPSEQ(field *DESCEND)` | 重复 Join 字段的排序 |
| `CONCAT(field1 field2)` | 拼接两字段 |
| `SST(field start len)` | 子字符串 |

## Select/Omit 关键字

```dds
*  Select 条件（只读符合条件的记录）
              S FIELD (value1 value2)    字段等于某值
              S FIELD (1000)             数值 > / < 用 COMP
              S FIELD COMP(*GT 1000)     大于 1000

*  Omit 条件（排除符合条件的记录）
              O FIELD ('X')
```

## CRTPF / CRTLF 命令行关键字

```bash
# 创建物理文件（指定记录长度）
CRTPF FILE(MYLIB/MYFILE) SRCFILE(MYLIB/QDDSSRC) +
     SIZE(10000)          # 初始成员记录数
     MAXMBRS(10)         # 最大成员数
     LVLCHK(*YES)        # 级别检查

# 创建逻辑文件
CRTLF FILE(MYLIB/MYLF) SRCFILE(MYLIB/QDDSSRC)
```

## 常用 DDS 布局示例

```dds
*=========================================================================
*  客户主文件 (PHYSICAL)
*=========================================================================
              R CUSTREC                   TEXT('客户主文件')
              K CUSTID                     主键: 客户号
              UNIQUE                        客户号唯一
              CUSTID       7P 0           TEXT('客户编号')
              CUSTNAME    50A             TEXT('客户名称')
              CUSTADDR   100A             TEXT('客户地址')
              CUSTTEL    15A             TEXT('联系电话')
              CREATE_DATE L               TEXT('创建日期')
              CREDIT_LIM 15P 2           TEXT('信用额度')
              STATUS       1A   DFT('A') TEXT('状态 A=有效 I=无效')
```

```dds
*=========================================================================
*  订单查询逻辑文件 (LOGICAL) — Select/Omit
*=========================================================================
              R CUSTORD                   PFILE(MYLIB/CUST)
              K CUSTID                     按客户号索引
              K CREATE_DATE                次键: 按日期
              S STATUS ('A')               只选有效客户
              O CREDIT_LIM (*ZERO)        排除信用额度为 0 的
```

## DDS vs RPG 数据类型映射

| DDS 类型 | RPG 类型 | 说明 |
|----------|---------|------|
| `P` (Packed) | `PACKED(n:m)` | 压缩十进制 |
| `S` (Zoned) | `ZONED(n:m)` | 区段十进制 |
| `B` (Binary) | `INT(n)` / `UINT(n)` | 整型 |
| `A` (Char) | `CHAR(n)` | 定长字符 |
| `L` (Date) | `DATE` | 日期 |
| `T` (Time) | `TIME` | 时间 |
| `Z` (Timestamp) | `TIMESTAMP` | 时间戳 |
| `H` (Hex) | `CHAR(n)` | 十六进制 |

## 相关页面

- [[dds-physical-files]] — 物理文件详解
- [[dds-logical-files]] — 逻辑文件（简单/Join/多格式）
- [[rpg-iv-data-types]] — RPG 数据类型
- [[rpg-file-processing]] — RPG 文件处理
