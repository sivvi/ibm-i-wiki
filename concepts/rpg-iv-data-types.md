---
title: RPG IV 数据类型
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, data-types]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG IV 数据类型

RPG IV 支持多种数据类型，分为**内部格式**（程序中的表示）和**外部格式**（文件/数据库中的表示）。

## 基本数据类型

| 数据类型 | 关键字 | 说明 | 示例 |
|----------|--------|------|------|
| 字符 | `CHAR(n)` | 定长字符串，右补空格 | `DCL-S name CHAR(30);` |
| 可变长字符 | `VARCHAR(n)` | 变长字符串（头部 2 字节长度） | `DCL-S body VARCHAR(1000);` |
| Unicode | `UCS-2(n)` | 双字节 Unicode | `DCL-S msg UCS_2(50);` |
| 整型 | `INT(n)` | 有符号整数（4/8 字节） | `DCL-S count INT(10);` |
| 无符号整型 | `UINT(n)` | 无符号整数 | `DCL-S flags UINT(10);` |
| 十进制 | `PACKED(n:m)` / `ZONED(n:m)` | BCD 压缩/区段格式 | `DCL-S amount PACKED(15:2);` |
| 浮点 | `FLOAT` | 单/双精度浮点 | `DCL-S ratio FLOAT;` |
| 日期 | `DATE` | ISO/USA/EUR/JUL 格式 | `DCL-S birth DATE;` |
| 时间 | `TIME` | HH:MM:SS | `DCL-S start TIME;` |
| 时间戳 | `TIMESTAMP` | YYYY-MM-DD-HH.MM.SS.uuuuuu | `DCL-S ts TIMESTAMP;` |
| 指示器 | `IND` | 布尔（`*ON`/`*OFF`） | `DCL-S found IND;` |
| 指针 | `POINTER` | 内存地址 | `DCL-S ptr POINTER;` |
| BLOB | `BLOB(n)` | 二进制大对象 | `DCL-S img BLOB(1M);` |
| CLOB | `CLOB(n)` | 字符大对象 | `DCL-S doc CLOB(10M);` |

## 数据结构（Data Structures）

数据结构用于将相关字段分组，与 COBOL 的"记录布局"类似。

### 命名数据结构

```rpgle
DCL-DS customer_t QUALIFIED;
    id CHAR(10);
    name CHAR(50);
    balance PACKED(15:2);
    email VARCHAR(100);
END-DS;

// 引用子字段
customer_t.id = 'C001';
customer_t.name = 'Acme Corp';
```

### 外部描述数据结构

```rpgle
DCL-DS Customer EXTNAME('CUSTOMER') END-DS;
```

### 带计数的变长数组

```rpgle
DCL-DS items_t QUALIFIED;
    count INT(10);
    item CHAR(50) DIM(999);
END-DS;

items_t.count = 5;
items_t.item(1) = 'First item';
```

## 枚举（Enumerations）

RPG IV 支持命名枚举类型，适合表示有限集合：

```rpgle
DCL-ENUM status_t;
    pending   0;
    active   1;
    closed   2;
END-ENUM;

DCL-S currentStatus INT(10);
currentStatus = pending;  // 0
```

## 命名常量

```rpgle
DCL-C MAX_RETRY  3;
DCL-C NULL_PTR   *NULL;
DCL-C CSV_DELIM  ',';
```

## 数组

```rpgle
// 静态数组
DCL-S prices PACKED(9:2) DIM(12);  // 12 个月的价格

// 动态变长数组
DCL-S tags VARCHAR(50) DIM(*) AUTO_ALLOC;
// 使用 %SPLIT 填充
tags = %SPLIT('rpg,ile,ibm-i' : ',');
```

## 表格和结果字段

```rpgle
// 表格（TABLES）
DCL-C DAYS_OF_WEEK;
DCL-S day_name CHAR(10) DIM(7) CTABLE('Mon':'Tue':'Wed':'Thu':'Fri':'Sat':'Sun');
DCL-S day_idx INT(10);

day_idx = 3;  // 结果字段
// day_name(day_idx) = 'Wed'

// 结果字段（RESULT_ADDR）
DCL-S result PACKED(9:2);
DCL-S basePrice PACKED(9:2) DIM(100) CTABLE(result);
```

## 指针类型

| 指针类型 | 说明 |
|----------|------|
| `POINTER` | 通用指针（未初始化） |
| `*BASED(ptrvar)` | 基于某指针的变量 |
| `*PTR` | 基础指针（赋值 `EVALP ptr = %ADDR(var)`） |
| `PROCPTR` | 过程指针 |

```rpgle
DCL-S ptr POINTER;
DCL-S target CHAR(20) INZ('data');
DCL-S basedVar CHAR(20) BASED(ptr);

ptr = %ADDR(target);  // ptr 指向 target
```

## 限定 vs 非限定数据

```rpgle
// 限定（QUALIFIED）— 必须用 DS.field 语法
DCL-DS ds1 QUALIFIED;
    f1 CHAR(10);
    f2 CHAR(10);
END-DS;
ds1.f1 = 'a';  // 必须加前缀

// 非限定 — 可直接用字段名
DCL-DS ds2;
    f1 CHAR(10);
    f2 CHAR(10);
END-DS;
f1 = 'a';  // 直接访问
```

## 存储规格与格式详解

### 内部格式 vs 外部格式

RPG IV 中，数值、字符、日期、时间戳字段都有**内部格式**（程序中的表示）和**外部格式**（文件/数据库中的表示）。

**内部格式**需要特别注意的场景：
- 按引用传递参数时
- 在数据结构中叠加子字段时

**默认内部格式：**
- 数值独立字段：压缩十进制（PACKED）
- 数值数据结构子字段：区段十进制（ZONED）
- 日期/时间字段：`*ISO`

### 字符数据类型（Character Data Type）

| 类型 | 字节数 | CCSID |
|------|--------|-------|
| 固定长度 CHAR | 声明长度 | 见下方CCSID规则 |
| 可变长 VARCHAR | 长度前缀(2/4字节)+数据 | 同上 |
| 指示器 IND | 1字节 | `*JOBRUN` |
| 图形 GRAPH | 2倍字符数 | 65535(*HEX)或EBCDIC双字节编码 |
| UCS-2 | 2倍字符数 | 13488 或 UTF-16 |

**VARCHAR 存储结构：**
```
| 当前长度(2/4字节) | 字符数据(N字节) |
|-------N+V bytes total, V=2 or 4-----|
```

### 数值数据类型（Numeric Data Type）

#### 二进制格式（Binary-Decimal / BINDEC）

- 符号位在最左侧，剩余位存储数值
- 正数符号位为0，负数为补码形式
- 长度1-9位：1-4位→2字节，5-9位→4字节
- 范围受限（如2字节字段最大99）

#### 浮点格式（Float / FLOAT）

由**尾数**和**指数**组成，公式：`尾数 × 10^指数`

| 类型 | 字节 | 有效位数 | 范围 |
|------|------|----------|------|
| 4字节Float | 4 | 8位 | -3.4028235E+38 ~ -1.1754944E-38, 0, +1.1754944E-38 ~ +3.4028235E+38 |
| 8字节Float | 8 | 16位 | -1.797693134862315E+308 ~ -2.225073858507201E-308, 0, +2.225073858507201E-308 ~ +1.797693134862315E+308 |

默认初始化值：`0E0`
注意：Float不适合需要精确小数位的场景（如货币），应使用PACKED。

#### 整型格式（Integer / INT）

| 字段长度 | 存储字节 | 范围 |
|----------|----------|------|
| 3位 | 1字节 | -128 ~ 127 |
| 5位 | 2字节 | -32768 ~ 32767 |
| 10位 | 4字节 | -2147483648 ~ 2147483647 |
| 20位 | 8字节 | -9223372036854775808 ~ 9223372036854775807 |

#### 无符号格式（Unsigned / UNS）

| 字段长度 | 存储字节 | 范围 |
|----------|----------|------|
| 3位 | 1字节 | 0 ~ 255 |
| 5位 | 2字节 | 0 ~ 65535 |
| 10位 | 4字节 | 0 ~ 4294967295 |
| 20位 | 8字节 | 0 ~ 18446744073709551615 |

#### 压缩十进制格式（Packed-Decimal / PACKED）

每个字节（除最低字节）可存储两个十进制数字。最低字节低4位存符号（F=正，D=负）。

**存储计算：** 数字位数 = `2n - 1`（n=使用的字节数），最大63位，最大32字节。

#### 区段十进制格式（Zoned-Decimal / ZONED）

每个字节存储一个数字或字符。低字节的区段部分表示符号（正=F，负=D）。

---

## 日期时间格式完整列表

### 日期格式（Date Formats）

#### 2位年份格式（范围：1940-2039）

| 格式 | 名称 | 默认分隔符 | 长度 | 示例 |
|------|------|------------|------|------|
| `*MDY` | 月/日/年 | / | 8 | 01/15/96 |
| `*DMY` | 日/月/年 | / | 8 | 15/01/96 |
| `*YMD` | 年/月/日 | / | 8 | 96/01/15 |
| `*JUL` | 儒略日 | / | 6 | 96/015 |

#### 4位年份格式（范围：0001-9999）

| 格式 | 名称 | 默认分隔符 | 长度 | 示例 |
|------|------|------------|------|------|
| `*ISO` | 国际标准 | - | 10 | 1996-01-15 |
| `*USA` | IBM USA标准 | / | 10 | 01/15/1996 |
| `*EUR` | IBM欧洲标准 | . | 10 | 15.01.1996 |
| `*JIS` | 日本工业标准 | - | 10 | 1996-01-15 |

#### 3位年份格式（范围：1900-2899）

| 格式 | 名称 | 格式 | 长度 | 示例 |
|------|------|------|------|------|
| `*CYMD` | 世纪年/月/日 | cyy/mm/dd | 9 | 101/04/25 |
| `*CMDY` | 世纪月/日/年 | cmm/dd/yy | 9 | 104/25/01 |
| `*CDMY` | 世纪日/月/年 | cdd/mm/yy | 9 | 125/04/01 |

#### 日期特殊值

| 格式 | *LOVAL | *HIVAL | 默认值 |
|------|--------|--------|--------|
| `*ISO` | 0001-01-01 | 9999-12-31 | 0001-01-01 |
| `*USA` | 01/01/0001 | 12/31/9999 | 01/01/0001 |
| `*EUR` | 01.01.0001 | 31.12.9999 | 01.01.0001 |
| `*JIS` | 0001-01-01 | 9999-12-31 | 0001-01-01 |

### 时间格式（Time Formats）

| 格式 | 名称 | 格式 | 默认分隔符 | 长度 | 示例 |
|------|------|------|------------|------|------|
| `*HMS` | 时:分:秒 | hh:mm:ss | : | 8 | 14:00:00 |
| `*ISO` | 国际标准 | hh.mm.ss | . | 8 | 14.00.00 |
| `*USA` | IBM USA标准 | hh:mm AM/PM | : | 8 | 02:00 PM |
| `*EUR` | IBM欧洲标准 | hh.mm.ss | . | 8 | 14.00.00 |
| `*JIS` | 日本工业标准 | hh:mm:ss | : | 8 | 14:00:00 |

#### 时间特殊值

| 格式 | *LOVAL | *HIVAL | 默认值 |
|------|--------|--------|--------|
| `*HMS` | 00:00:00 | 24:00:00 | 00:00:00 |
| `*ISO` | 00.00.00 | 24.00.00 | 00.00.00 |
| `*USA` | 00:00 AM | 12:00 AM | 00:00 AM |

### 时间戳格式（Timestamp）

- 格式：`YYYY-MM-DD-hh.mm.ss.ffffff`（默认6位小数秒）
- 范围：0-12位小数秒
- 默认初始化值：`0001-01-01-00.00.00.000000`
- 字面量：`Z'2025-12-25-01.02.03.123456'`

---

## 编辑码（Edit Codes）

编辑码用于按预定义模式编辑数值字段。分为三类：**简单编辑码（X, Y, Z）**、**组合编辑码（1-4, A-D, J-Q）**、**用户定义编辑码（5-9）**。

### 简单编辑码

| 编辑码 | 功能 |
|--------|------|
| X | 保留十六进制F符号（正）或D符号（负），不压缩前导零 |
| Y | 编辑3-9位日期字段，压缩前导零，插入斜杠分隔符（不适用于*YEAR/*MONTH/*DAY） |
| Z | 移除符号，压缩所有前导零，不显示小数点 |

### 组合编辑码（1-15）

| 编辑码 | 分组 | 小数点 | 负数指示 | 零抑制 |
|--------|------|--------|----------|--------|
| 1 | Yes | Yes | 无符号 | Yes |
| 2 | Yes | Yes | 无符号 | Yes（显示空格）|
| 3 | Yes | No | 无符号 | Yes |
| 4 | Yes | No | 无符号 | Yes（显示空格）|
| A | Yes | Yes | CR | Yes |
| B | Yes | Yes | CR | Yes（显示空格）|
| C | Yes | No | CR | Yes |
| D | Yes | No | CR | Yes（显示空格）|
| J | Yes | Yes | - (减号) | Yes |
| K | Yes | Yes | - (减号) | Yes（显示空格）|
| L | Yes | No | - (减号) | Yes |
| M | Yes | No | - (减号) | Yes（显示空格）|
| N | Yes | Yes | - (浮动减号) | Yes |
| O | Yes | Yes | - (浮动减号) | Yes（显示空格）|
| P | Yes | No | - (浮动减号) | Yes |
| Q | Yes | No | - (浮动减号) | Yes（显示空格）|

### 3位数编辑码（用户定义）

用户可通过 CL 命令 CRTEDTD 创建3位数编辑码（500-999），或使用预定义的5-9编辑码。

### 编辑码示例

对于数值 `1234567`（2位小数）和 `-00012.5`（3位小数）：

| 编辑码 | 正数(2位小数) | 负数(3位小数) | 零值 |
|--------|---------------|--------------|------|
| 无 | 1234567 | 00012.5 | 000000 |
| 1 | 12,345.67 | .120- | 0 |
| 2 | 12,345.67 | (空格) | (空格) |
| A | 12,345.67 | .120CR | 0 |
| J | 12,345.67 | .120- | 0 |
| Z | 1234567 | 12.5 | (空格) |

---

## NULL 值支持

### ALWNULL 选项

在控制规范上使用 ALWNULL 关键字控制对外部描述数据库文件中 NULL 字段的处理：

| 选项 | 说明 |
|------|------|
| `*USRCTL` | 完整支持：读写/更新/删除包含NULL值的记录，可对NULL键定位 |
| `*INPUTONLY` | 仅输入：读取NULL值记录以访问字段数据 |
| `*NO` | 不处理NULL值记录 |

### NULL 处理规则

**当 ALWNULL(*USRCTL) 时：**
- 使用 `%NULLIND(field)` 函数检测字段是否为 NULL（右表达式）
- 使用 `%NULLIND(field) = *ON` 设置字段为 NULL（左表达式）
- 可通过 KFLD 操作中的 factor2 指示器对 NULL 键字段进行定位

**输入时的行为：**
- 读取 NULL 字段时，关联的 NULL 指示器被设置为 *ON
- NULL 指示器在程序初始化时默认为 *OFF
- NULL 字段不能作为匹配字段或控制级字段

**输出时的行为：**
- 当 NULL 指示器为 *ON 时，输出 NULL 值到数据库
- NULL 字段输出时数据仍会被移动到缓冲区（可能触发错误）

**示例：**
```rpgle
// 读取并更新包含NULL字段的记录
READ REC;
IF NOT %NULLIND(Fld1);
   Fld1 = 'New value';
ENDIF;
%NULLIND(Fld2) = *ON;  // 设置为NULL
UPDATE REC;
```

---

## CCSID 转换规则

### CCSID 基础

所有字符、图形和 UCS-2 数据都有 CCSID（编码字符集标识）。例如：
- UTF-8 CCSID 1208：`x'20'` = 空格
- EBCDIC CCSID 37：`x'40'` = 空格
- UCS-2 CCSID 13488：`x'0020'` = 空格

### 隐式转换发生的场景

1. **比较操作**：使用 Unicode CCSID 进行比较
2. **赋值操作**：源值转换为目标类型和 CCSID
3. **按值传递和只读引用传递**：参数转换为原型参数的 CCSID
4. **连接操作**：操作数转换为 Unicode CCSID（除非一个是十六进制字面量）

### Built-in Function 的隐式 CCSID 转换

| 函数 | 转换规则 |
|------|----------|
| %CHECK, %CHECKR | 比较字符串转换为被检查字符串的 CCSID |
| %LOOKUPxx | 搜索参数转换为数组的 CCSID |
| %REPLACE, %SCAN, %SCANR, %SCANRPL | 替换/搜索字符串转换为源字符串的 CCSID |
| %SPLIT | 分隔符字符串转换为被分割字符串的 CCSID |
| %TRIM, %TRIML, %TRIMR | 修剪字符串转换为被修剪字符串的 CCSID |
| %XLATE | from/to 字符串转换为源字符串的 CCSID |

### 转换优先级（Unicode CCSID 确定）

1. 任一操作数是 character 类型 → UTF-8
2. 都不是 UCS-2 类型 → 模块的默认 UCS-2 CCSID（默认 13488）
3. 只有一个是 UCS-2 → 转换为 UCS-2 操作数的 CCSID
4. 两个都是 UCS-2 → 使用默认 UCSID 或转换较短长度到较长长度

### 输入/输出时的 CCSID 转换

**转换发生的条件：**
- 程序字段 CCSID 不是 65535(*HEX)
- 文件字段 CCSID 不是 65535
- 缓冲区 CCSID 与字段 CCSID 不同

**不发生转换的情况：**
- `DATA(*NOCVT)` 对文件生效
- 作业 CCSID 为 65535

### 特殊 CCSID 值

| 值 | 说明 |
|----|------|
| `*HEX` / 65535 | 十六进制，无 CCSID 转换 |
| `*JOBRUN` | 使用作业 CCSID |
| `*JOBRUNMIX` | 作业相关的混合 SBCS/DBCS CCSID |
| `*UTF8` | UTF-8 (CCSID 1208) |
| `*UTF16` | UTF-16 (CCSID 1200) |

---

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[rpg-subprocedures]] — 子过程与函数
- [[rpg-file-processing]] — 文件处理
- [[ile-concepts]] — ILE 架构
