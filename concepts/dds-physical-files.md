---
title: DDS 物理文件
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [dds, ibm-i, database]
sources: [raw/papers/dds-physical-logical-7.5.txt]
---

# DDS 物理文件

DDS（Data Description Specification）是 IBM i 数据库文件的核心定义语言。物理文件（`*FILE`）在 DB2 for i 中存储实际数据。

## DDS 源码格式（位置 1-80）

每行 DDS 源码分 6 个区域：

```
位置   内容                    说明
1-5    序号                    可选，用于文档
6      A                       固定为 A（表示 DDS 表单行）
7      *                      注释（可选）
8-16   条件                   （留空）
17     R/K                    R=记录格式名，K=键字段名，空=字段名
18     （保留）               留空
19-28  名称                   记录格式/字段/键名
29     REF                     引用外部文件（可选）
30-34  长度                   字段字节数（右对齐）
35     数据类型               P/S/B/F/A/H/L/T/Z/5 等
36-37  小数位                 数字字段的小数位数（0-63）
38     用途                   I/O/B（输入/输出/两者）
39-44  位置                   子字符串/拼接起始位置
45-80  关键字                 字段级/文件级关键字
```

## 数据类型（Position 35）

| 代码 | 类型 | 说明 | 默认长度 |
|------|------|------|---------|
| `A` | Character | 字符 | 1-32766 |
| `P` | Packed Decimal | 压缩十进制 | 1-63 位 |
| `S` | Zoned Decimal | 区段十进制 | 1-63 位 |
| `B` | Binary | 二进制 | 1-18 位 |
| `F` | Floating-point | 浮点 | 单/双精度 |
| `H` | Hexadecimal | 十六进制（不转码） | 1-32766 |
| `L` | Date | 日期 | 系统决定 |
| `T` | Time | 时间 | 8 字符 |
| `Z` | Timestamp | 时间戳 | 26 字符 |
| `5` | Binary Character | 二进制字符 | 1-32766 |

> **注意**：`P`（压缩十进制）算术运算最快。留空位置 35 时：位置 36-37 有数字 → 默认 `P`，否则默认 `A`。

## 存储字节计算

| 数据类型 | 存储字节 |
|----------|---------|
| Character / Hexadecimal / Binary Char | 等于长度 |
| Zoned Decimal | 等于位数 |
| Packed Decimal | `位数/2 + 1`（向上取整） |
| Binary 1-4位 | 2 字节 |
| Binary 5-9位 | 4 字节 |
| Binary 10-18位 | 8 字节 |
| Float (single) | 4 字节 |
| Float (double) | 8 字节 |
| Date | 6/8/10 字符（取决于 DATFMT） |
| Time | 8 字符 |
| Timestamp | 26 字符 |

## 物理文件示例

```dds
*=========================================================================
*  物理文件: ORDER  (订单主文件)
*=========================================================================
              R ORDFMT                     TEXT('订单记录格式')
              ORDNBR      10A             TEXT('订单号')
              CUSTID       7P 0           TEXT('客户编号')
              ORDDATE      L              TEXT('订单日期')
              ORDAMT      15P 2          TEXT('订单金额')
              STATUS       2A             TEXT('状态')
              DFT('A')                      STATUS 默认值
              UNIQUE                           键值唯一
```

## 键字段（Key Fields）

键字段决定记录的物理存储顺序：

```dds
              R ORDFMT
              K ORDNBR                       按订单号升序排列
              K ORDDATE                      第二键，按日期
              K CUSTID                       第三键，按客户
              ORDNBR      10A
              CUSTID       7P 0
              ORDDATE      L
```

**UNIQUE 关键字**：要求键值唯一（否则报错/覆盖）。

## 常用文件级关键字

| 关键字 | 说明 |
|--------|------|
| `UNIQUE` | 记录键值必须唯一 |
| `REF(lib/file)` | 引用另一文件的字段定义 |
| `TEXT('description')` | 文件描述文本 |
| `CCSID(number)` | 编码字符集标识 |
| `FRCATR(*NONE)` | 不允许记录进入参考文件 |
| `LIFO` / `FIFO` / `FCFO` | 溢出处理方式 |
| `ALTSEQ(*NONE)` | 不使用替代排序序列 |

## 常用字段级关键字

| 关键字 | 说明 |
|--------|------|
| `TEXT('description')` | 字段描述 |
| `DFT(default)` | 默认值 |
| `REFFLD(file/field)` | 引用字段定义 |
| `COLHDG('heading')` | 列标题 |
| `DATFMT(*USA/*ISO/*EUR/*JIS/*JOB)` | 日期格式 |
| `DATSEP(*SLASH/*DASH/*PERIOD/*JOB)` | 日期分隔符 |
| `CHECK(AB/ME/MF/M10...)` | 校验码检验 |
| `COMP(*EQ/*NE/*GT...)` | 比较条件 |
| `RANGE(low:high)` | 取值范围 |
| `VALUES('a':'b':'c')` | 允许值列表 |
| `ALWNULL` | 允许 NULL 值 |
| `VARLEN` | 可变长度字段 |

## 创建物理文件

```bash
# 从 DDS 源码创建物理文件
CRTPF FILE(MYLIB/ORDER) SRCFILE(MYLIB/QDDSSRC) SRCMBR(ORDER)

# 带记录长度
CRTPF FILE(MYLIB/ORDER) SRCFILE(MYLIB/QDDSSRC) SIZE(*MAX4GB)

# 查看结构
DSPFFD FILE(MYLIB/ORDER)
```

## 字段引用（REF / REFFLD）

```dds
* 引用另一文件的字段定义
              R ORDFMT                     REF(COMMON/FIELDS)
              K ORDNBR                       覆盖键字段
              ORDTYPE    5A   DFT('O')     覆盖默认值
```

## 与 RPG 的集成

物理文件创建后，RPG 可以直接用 `EXTNAME` 引用：

```rpgle
**FREE
DCL-DS Order EXTNAME('ORDER') END-DS;
// Order.ORDNBR、Order.CUSTID 等字段直接可用
```

## 相关页面

- [[dds-logical-files]] — 逻辑文件（简单/多格式/Join）
- [[dds-keywords]] — DDS 关键字速查
- [[rpg-file-processing]] — RPG 文件处理
- [[ibm-i-platform]] — IBM i 平台
