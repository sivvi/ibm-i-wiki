---
title: RPG 子过程与函数
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, subprocedures]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG 子过程与函数

子过程（Subprocedures）和子例程（Subroutines）是 RPG 中模块化代码的两种方式。子过程是 ILE 的核心特性，比子例程更强大。

## 子过程 vs 子例程

| 特性 | 子例程（EXSR） | 子过程 |
|------|----------------|--------|
| 作用域 | 全局 | 可私有（local） |
| 参数传递 | 通过 PLIST | 原型接口（PI） |
| 返回值 | 无 | 有（`RETURN` 语句） |
| 编译单元 | 主程序内 | 独立模块 |
| 递归 | 不支持 | 支持 |
| ILE 支持 | 否 | 是 |

## 子过程定义

```rpgle
**FREE
// 子过程定义
DCL-PROC calculateTotal;
    DCL-PI calculateTotal PACKED(15:2);
        quantity PACKED(5:0) CONST;
        unitPrice PACKED(9:2) CONST;
    END-PI;

    DCL-S lineTotal PACKED(15:2);

    lineTotal = quantity * unitPrice;
    RETURN lineTotal;
END-PROC calculateTotal;

// 调用
DCL-S result PACKED(15:2);
result = calculateTotal(10 : 29.99);
```

## 原型（Prototype）

原型定义过程的接口，在编译时用于参数类型检查：

```rpgle
**FREE
// 原型 — 通常放在 COPY 文件中共享
DCL-PR calculateTotal PACKED(15:2) EXTPROC('CALCTOT');
    pQuantity PACKED(5:0) CONST;
    pUnitPrice PACKED(9:2) CONST;
END-PR;

// 调用点
result = calculateTotal(10 : 29.99);
```

### 原型参数修饰符

| 修饰符 | 说明 |
|--------|------|
| `CONST` | 常量传递（副本，只读） |
| `VALUE` | 值传递 |
| `TEMPORARY` | 临时变量 |
| `OPTIONS(*Omissible)` | 可选参数 |

## 过程接口（Procedure Interface）

```rpgle
DCL-PI myProc;
    inputData CHAR(100) CONST;
    outputData CHAR(100);
    errorCode INT(10);
END-PI;
```

## 过程包（Procedure Package）与服务程序

子过程编译为模块（`*MODULE` 对象），多个模块可绑定为**服务程序**（`*SRVPGM`）：

```rpgle
// 编译: CRTRPGMOD MODULE(MYLIB/MYPROC) SRCFILE(MYLIB/QRPGLESRC)
// 绑定: CRTSRVPGM SRVPGM(MYLIB/MYSRV) MODULE(MYLIB/MYPROC)
```

## 递归子过程

```rpgle
DCL-PROC factorial;
    DCL-PI factorial PACKED(15:0);
        n INT(10) CONST;
    END-PI;

    IF n <= 1;
        RETURN 1;
    ENDIF;

    RETURN n * factorial(n - 1);
END-PROC factorial;
```

## 调用方式

### 程序调用（CALL / CALLP）

```rpgle
// CALL — 运行时决定（兼容性）
CALL 'MYPROG';
CALLP myProc(param1 : param2);

// CALLP — 编译时检查（推荐）
CALLP myProc(param1 : param2);
```

### 表达式内调用

```rpgle
DCL-S result PACKED(15:2);
result = calculateTotal(qty : price);  // 直接在表达式中
```

## 参数校验

使用 `%-VALIDRANGE` 等 BIF 配合 `ERROR` 指示器：

```rpgle
DCL-PR myFunc INT(10) OPINFO;
    p1 INT(10) VALUE;
END-PR;
```

## 主过程与 NOMAIN 模块

### 带主过程

```rpgle
**FREE
// 这是一个有主程序的模块（可执行）
Dcl-s counter INT(10);
counter = 0;
*INLR = *ON;
```

### NOMAIN（纯工具模块）

```rpgle
**FREE
// NOMAIN 模块 — 只含子过程，不能独立运行
// 编译: CRTRPGMOD MODULE(LIB/MYLIB) SRCFILE(LIB/QRPGLESRC)
//       ACTGRP(*NEW) 允许 NOMAIN
DCL-PROC utilFunc;
    DCL-PI utilFunc CHAR(50);
    END-PI;
    RETURN 'result';
END-PROC utilFunc;
```

## 常见错误

- `RNF5408`: 参数数量/类型不匹配 — 检查原型定义
- `RNF7030`: 递归调用但过程未声明为递归
- `RNF5407`: 引用未定义的过程 — 检查绑定目录（BNDDIR）

## 相关页面

- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[ile-concepts]] — ILE 架构
- [[rpg-error-handling]] — 错误处理
- [[service-programs]] — 服务程序
