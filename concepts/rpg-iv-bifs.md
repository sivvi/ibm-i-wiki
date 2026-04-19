---
title: RPG IV 内置函数（BIFs）
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, built-in-functions]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG IV 内置函数（BIFs）

内置函数（Built-in Functions，BIFs）在表达式中使用，返回标量值或特殊结果。每个 BIF 以 `%` 开头。

## 字符串处理

### %TRIM / %TRIML / %TRIMR

去除字符串两端/左/右的空格和制表符。

```rpgle
DCL-S name VARCHAR(50);
DCL-S greeting CHAR(100);
name = '  Hello World  ';
greeting = 'Say: ' + %TRIM(name);  // 'Say: Hello World'
```

### %CHAR

将数值、日期或时间转换为字符格式。

```rpgle
DCL-S num INT(10);
DCL-S str CHAR(20);
num = 42;
str = %CHAR(num);  // '42'
```

### %DEC

将字符或数值表达式转换为压缩十进制格式。

```rpgle
DCL-S packed DEC(7:2);
packed = %DEC('123.45' : 7 : 2);  // 123.45
```

### %SPLIT

将字符串按分隔符拆分为数组。

```rpgle
DCL-S parts VARCHAR(50) DIM(10);
DCL-S count INT(10);
parts = %SPLIT('a,b,c' : ',');  // {'a' 'b' 'c'}
count = %ELEM(parts);
```

## 文件状态检查

### %EOF

返回文件是否到达末尾（`*ON` 或 `*OFF`）。

```rpgle
DOW NOT %EOF(CustomerFile);
    READ CustomerFile;
    IF NOT %EOF(CustomerFile);
        // 处理记录
    ENDIF;
ENDDO;
```

### %FOUND

返回文件操作（CHAIN、READ、SETGT 等）是否找到匹配记录。

```rpgle
CHAIN (custId) CustomerFile;
IF %FOUND(CustomerFile);
    // 找到记录
ELSE;
    // 未找到
ENDIF;
```

### %OPEN

返回指定文件当前是否处于打开状态。

```rpgle
IF NOT %OPEN(MyFile);
    OPEN MyFile;
ENDIF;
```

## 日期和时间

### %DATE / %TIME / %TIMESTAMP

创建和转换日期/时间/时间戳值。

```rpgle
DCL-S today DATE;
DCL-S now TIMESTAMP;
today = %DATE();           // 当前系统日期
now = %TIMESTAMP();        // 当前时间戳
```

### %DIFF

计算两个日期/时间之间的差异。

```rpgle
DCL-S daysDiff PACKED(5:0);
daysDiff = %DIFF(%DATE() : someDate : *DAYS);
```

### %SUBDT

从日期/时间/时间戳中提取子单元（年、月、日、时、分、秒）。

```rpgle
DCL-S year SMALLINT;
year = %SUBDT(someDate : *YEARS);
```

## 数值函数

### %ABS

返回数值的绝对值。

```rpgle
DCL-S delta PACKED(7:2);
delta = %ABS(-123.45);  // 123.45
```

### %SIZE

返回变量、字段或数据类型的字节大小。

```rpgle
DCL-S arr INT(10) DIM(100);
DCL-S arrSize INT(10);
arrSize = %SIZE(arr);  // 400 (100 * 4 bytes)
```

### %PARMS

返回传递给当前程序或过程的参数个数。

```rpgle
DCL-PR myProc;
    parm1 CHAR(10);
    parm2 INT(10);
END-PR;
```

## 指针和地址

### %ADDR

返回变量的地址（用于指针操作）。

```rpgle
DCL-S ptr POINTER;
DCL-S name CHAR(20);
ptr = %ADDR(name);
```

### %PADDR

返回程序或过程的地址。

```rpgle
DCL-PR procPtr POINTER EXTPROC('MYPROC');
END-PR;
ptr = %PADDR(myProc);
```

## JSON 支持

### %JSON

将 RPG 数据结构解析为 JSON，或将 JSON 字符串转为 RPG 数据结构。

```rpgle
DCL-DS jsonObj CHAR(500);
DCL-DS customer_t QUALIFIED;
    id CHAR(10);
    name CHAR(50);
    balance PACKED(15:2);
END-DS;

jsonObj = '{"id":"C001","name":"Acme","balance":1234.56}';
customer_t = %JSON.parse(jsonObj);
```

## 完整 BIF 列表（按字母序）

| BIF | 用途 |
|-----|------|
| `%ABS` | 绝对值 |
| `%ADDR` | 取地址 |
| `%CHAR` | 转字符 |
| `%DATE` | 日期构造/转换 |
| `%DEC` | 转十进制 |
| `%DIFF` | 日期间差 |
| `%EOF` | 文件末尾 |
| `%EQUAL` | 精确匹配 |
| `%FOUND` | 找到记录 |
| `%JSON` | JSON 解析/生成 |
| `%LOOKUP` | 数组查找 |
| `%OPEN` | 文件打开 |
| `%PADDR` | 过程地址 |
| `%PARMS` | 参数个数 |
| `%SIZE` | 字节大小 |
| `%SPLIT` | 字符串拆分 |
| `%STATUS` | 文件/程序状态 |
| `%STR` | null 终止字符串 |
| `%SUBDT` | 日期子单元 |
| `%TIME` | 时间构造/转换 |
| `%TIMESTAMP` | 时间戳 |
| `%TRIM` / `%TRIML` / `%TRIMR` | 去除空格 |
| `%XLATE` | 字符转换 |

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-data-types]] — 数据类型
- [[rpg-file-processing]] — 文件处理
- [[rpg-json-sql]] — JSON 与 SQL
