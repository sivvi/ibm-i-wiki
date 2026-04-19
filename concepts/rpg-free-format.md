---
title: RPG Free Format
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-free]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG Free Format

RPG Free Format（自由格式）从 RPG IV V3R7 开始引入，允许用自由格式书写代替传统的固定列格式规格说明（fixed-form specifications）。

## 与固定格式的对比

### 固定格式（Column-dependent）

```
     FMyFile    IF   E           DISK
     C           Z-ADD 0       counter
     C           DOUBEQ    'Y'
     C           ADD       1       counter
     C           ENDDO
     C           SETON                                        LR
```

### 自由格式（Free-form）

```rpgle
**FREE
DCL-F MyFile DISK(*INPUT);
DCL-S counter INT(10) INZ(0);
DCL-S matchFlag CHAR(1);

READ MyFile;
DOW NOT %EOF(MyFile);
    IF matchFlag = 'Y';
        counter += 1;
    ENDIF;
    READ MyFile;
ENDDO;
*INLR = *ON;
```

## 声明关键字（DCL-*）

| 固定格式 | Free Format |
|----------|-------------|
| `DCL-S` | `DCL-S` — 变量声明 |
| `DCL-DS` | `DCL-DS ... END-DS` — 数据结构 |
| `DCL-PR` | `DCL-PR ... END-PR` — 原型定义 |
| `DCL-PI` | `DCL-PI ... END-PI` — 过程接口 |
| `DCL-C` | `DCL-C` — 命名常量 |
| `DCL-E` | `DCL-E ... END-E` — 枚举 |
| `DCL-ENUM` | `DCL-ENUM ... END-ENUM` — 枚举 |
| `DCL-OR` | `DCL-OR` — 联合数据结构 |
| `DCL-SUBF` | `DCL-SUBF` — 子字段声明 |
| `DCL-PROC` | `DCL-PROC ... END-PROC` — 子过程 |
| `END-PROC` | `END-PROC` |
| `DCL-F` | `DCL-F` — 文件声明 |
| `Ctl-opt` | `Ctl-opt` — 控制选项 |

## 操作码（Free-format Operations）

自由格式中，操作码的写法：

```rpgle
// 赋值
counter = 0;
total = base + tax;

// 条件
IF status = 'A';
    process();
ELSEIF status = 'B';
    skip();
ELSE;
    abort();
ENDIF;

// 循环
FOR i = 1 TO 10;
    total *= i;
ENDFOR;

DOW condition;
    READ file;
ENDDO;

FOR-EACH item IN collection;
    process(item);
ENDFOR;

// 选择性返回
SELECT;
    WHEN code = 1;
        handleOne();
    WHEN code = 2;
        handleTwo();
    OTHER;
        handleOther();
ENDSL;

// 例外处理
ON-ERROR 12111;
    handleError();
END-CVSR;
```

## 规格说明行标记

| 标记 | 说明 |
|------|------|
| `**FREE` | 整个源文件使用自由格式 |
| `**FIXED` | 整个源文件使用固定格式 |
| `/FREE` | 此处开始自由格式，后续可混用 `/END-FREE` 切回 |
| `/END-FREE` | 切回固定格式 |

## 控制选项（Ctl-opt）

```rpgle
**FREE
Ctl-opt DFTACTGRP(*NO) ACTGRP(*NEW);
Ctl-opt BNDDIR('MYBNDDIR');
Ctl-opt OPTION(*NOUNREF : *NODEBUGIO);
Ctl-opt TGTRLS(V7R4M0);
```

## 文件声明

```rpgle
// 简单顺序文件
DCL-F MyFile DISK;

// 带键值的顺序/索引文件
DCL-F CustomerFile KEYED INFLD(custId) USAGE(*INPUT : *OUTPUT);

// PRINTER 文件
DCL-F Report PRINTER(132);

// WORKSTN (显示文件)
DCL-F Display E DISK EXTFILE('MYLIB/DSPF001') USAGE(*OUTPUT : *INPUT : *UPDATE);
```

## 嵌套数据结构

```rpgle
DCL-DS customer_t QUALIFIED;
    id CHAR(10);
    address_t QUALIFIED;
        street CHAR(50);
        city CHAR(30);
        zip CHAR(10);
    END-DS;
    balance PACKED(15:2);
END-DS;

customer_t.id = 'C001';
customer_t.address_t.city = 'Shanghai';
```

## 优缺点

**优点：**
- 代码更简洁、可读性更好
- 更容易与现代 IDE/编辑器集成
- 支持更自然的命名约定

**缺点：**
- 需要 RPG IV（V3R7+）
- 部分老式操作码（如 `MHHZO`、`MLHZO`）无自由格式等价物
- 学习曲线：习惯固定格式的人需要适应

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[rpg-subprocedures]] — 子过程
- [[ile-concepts]] — ILE 架构
- [[rpg-file-processing]] — 文件处理
