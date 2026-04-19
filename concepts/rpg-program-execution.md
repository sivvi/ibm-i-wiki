---
title: RPG 程序运行与 CL 集成
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, ibm-i, compilation]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
---

# RPG 程序运行与 CL 集成

## 运行程序

### 基本调用

```bash
CALL PGM(MYLIB/MYPROG)
```

### 带参数运行

RPG 程序参数通过 PARM 传递，与 RPG 的 `*PARM` 列表对应：

```bash
# CL 端
CALL PGM(MYLIB/CALC) PARM(100 29.95)
```

RPG 端接收：

```rpgle
**FREE
DCL-PI main;
    quantity PACKED(5:0);
    unitPrice PACKED(9:2);
END-PI;

DCL-S total PACKED(15:2);
total = quantity * unitPrice;
Dsply ('Total: ' + %CHAR(total));
*INLR = *ON;
```

## CL 与 RPG 的交互

### 从 CL 调用 RPG

**传统 OPM 方式：**
```cl
PGM        PARM(&CUSTID &AMOUNT)
DCL       &CUSTID *CHAR 10
DCL       &AMOUNT *DEC 15 2
CALL      MYLIB/MYRPGPGM PARM(&CUSTID &AMOUNT)
ENDPGM
```

**ILE 方式（推荐）：**
```cl
CALLPRC   PRC(MYLIB/MYRPGPRC) PARM(&CUSTID &AMOUNT) RTNVAR(&RESULT)
```

### 从 RPG 调用 CL

RPG 本身不直接调用 CL 命令，但可以通过两种方式间接执行：

**1. QCMDEXC — 执行命令字符串：**
```rpgle
**FREE
DCL-PR QCMDEXC EXTPGM('QCMDEXC');
    command POINTER CONST;
    length PACKED(15:5) CONST;
END-PR;

DCL-S cmd VARCHAR(500);
cmd = 'CLRPFM FILE(MYLIB/MYFILE) MBR(*FIRST)';
QCMDEXC(%ADDR(cmd) : %LEN(%TRIM(cmd)));
```

**2. QCAPCMD — 带选项的命令执行：**
```rpgle
DCL-PR QCAPCMD EXTPGM('QCAPCMD');
    command POINTER CONST;
    length INT(15) CONST;
    options CHAR(100) CONST;
    exitProgram CHAR(20) CONST;
END-PR;
```

## 程序对象管理

### 查看程序信息

```bash
DSPPGM PGM(MYLIB/MYPGM)

# 显示签名
DSPPGM PGM(MYLIB/MYPGM) DETAIL(*SIGNATURE)

# 显示模块列表
DSPPGM PGM(MYLIB/MYPGM) DETAIL(*MODULES)
```

### 列出模块

```bash
DSPOBJD OBJ(MYLIB/MYPGM) OBJTYPE(*PGM)
```

### 重置程序（清除静态变量）

```bash
RCLPGM PGM(MYLIB/MYPGM)
```

## 激活组管理

### 为什么要用激活组

激活组决定了程序运行时的资源边界——同一激活组内的程序共享静态存储和文件句柄：

```
*DFTACTGRP (默认)   → 传统行为，全局共享
*NEW                → 每次调用独立，结束时释放
命名激活组          → 自定义生命周期
```

### 指定激活组

```rpgle
**FREE
Ctl-opt DFTACTGRP(*NO) ACTGRP('MYAPP');
```

### CL 中运行

```cl
CALL PGM(MYLIB/MYPGM) ACTGRP(*NEW)
```

### 释放激活组

```bash
RCLACTGRP ACTGRP(MYAPP)
```

## 编译命令对比

| 命令 | 类型 | 适用场景 |
|------|------|---------|
| `CRTBNDRPG` | 一步编译运行 | 快速开发/原型 |
| `CRTRPGMOD` | 编译为模块 | 需多模块/服务程序 |
| `CRTSQLRPG` | 编译含 SQL 的 RPG | 数据库应用 |
| `CRTSQLRPGI` | 编译 IFS 源码含 SQL | IFS 源文件 |

## CRTBNDRPG 常用参数

```bash
CRTBNDRPG PGM(MYLIB/MYPGM)
     SRCFILE(MYLIB/QRPGLESRC)
     SRCMBR(MYPGM)
     DFTACTGRP(*NO)       # 脱离默认组
     ACTGRP(*NEW)         # 新激活组
     DBGVIEW(*LIST)       # 调试视图
     OPTION(*NOUNREF)     # 报告未用变量
     TGTRLS(V7R4M0)       # 目标版本
     STGMDL(*SNGLVL)      # 存储模型
```

## 程序返回码

RPG 程序通过设置 `*INLR = *ON` 表示正常结束，可选通过数据区返回状态：

```rpgle
**FREE
*INLR = *ON;       // 正常返回
*INRT = *ON;       // 退回（相当于 RETURN）
```

## 常见问题

- **程序调用时 MCH0601**：参数长度/类型不匹配
- **程序不退出**：检查 `*INLR` 是否设置
- **文件句柄泄漏**：确认激活组是否正确释放
- **CL 命令找不到**：用 `*LIBL` 或绝对路径指定库

## 相关页面

- [[ile-concepts]] — ILE 架构
- [[rpg-binding-strategies]] — 绑定策略
- [[rpg-debugging]] — 程序调试
- [[ibm-i-platform]] — IBM i 平台
