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

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[rpg-subprocedures]] — 子过程与函数
- [[rpg-file-processing]] — 文件处理
- [[ile-concepts]] — ILE 架构
