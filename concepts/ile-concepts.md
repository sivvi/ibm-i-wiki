---
title: ILE 集成语言环境
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [ibm-i, ile]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# ILE 集成语言环境

ILE（Integrated Language Environment，集成语言环境）是 IBM i 操作系统提供的编程模型，允许不同语言（C、RPG、COBOL、CL）的程序相互调用并共享过程。

## 核心概念

### 模块（Module）

模块是编译单元的最小对象。通过 `CRTRPGMOD`、`CRTCMOD` 等命令创建。

```bash
CRTRPGMOD MODULE(MYLIB/HELLOMOD) SRCFILE(MYLIB/QRPGLESRC)
```

### 服务程序（Service Program）

服务程序是多个已编译模块的绑定集合（`.so`/`.dll` 类似物）。优势在于：

- 多个程序共享同一份代码副本（节省内存）
- 无需重新绑定即可更新逻辑
- 通过签名（signature）保证兼容性

```bash
CRTSRVPGM SRVPGM(MYLIB/UTILSRV) MODULE(MYLIB/STRINGS MYLIB/DATES)
```

### 程序（Program）

程序是可直接调用执行的对象。通过绑定模块和服务程序创建：

```bash
CRTPGM PGM(MYLIB/MYPROG) MODULE(MYLIB/MAINMOD) BNDDIR(MYLIB/UTILS)
```

### 激活组（Activation Group）

激活组管理程序运行时的资源（文件句柄、静态变量、事务管理等）：

| 激活组 | 说明 |
|--------|------|
| `*DFTACTGRP`（默认） | 传统单线程组 |
| `*NEW` | 每次调用创建新组，结束时销毁 |
| `*CALLER` | 继承调用方的组 |

```rpgle
Ctl-opt DFTACTGRP(*NO) ACTGRP(*NEW);
```

## 绑定方式

### 1. 绑定目录（Binding Directory）

```bash
CRTBNDDIR BNDDIR(MYDIR)
ADDBNDDIRE BNDDIR(MYDIR) OBJ((*LIBL/STRINGS) (*LIBL/DATES))
```

```rpgle
Ctl-opt BNDDIR('MYDIR');
```

### 2. 过程签名（Procedure Export）

通过 `EXTPROC` 导出：

```rpgle
DCL-PR myFunc INT(10) EXTPROC('MYFUNC');
    p1 CHAR(10) VALUE;
END-PR;
```

### 3. 绑定到服务程序

```bash
CRTSRVPGM SRVPGM(LIB/UTILS) MODULE(LIB/UTIL1 LIB/UTIL2) EXPORT(*SRCFILE)
```

## 调用链

```
调用方程序 (PGM)
  └─ 调用过程 (PROC) in 模块 (MODULE)
        └─ 调用外部服务程序 (SRVPGM)
              └─ 模块 (MODULE)
```

## 关键命令

| 命令 | 用途 |
|------|------|
| `CRTRPGMOD` | 编译 RPG 源为模块 |
| `CRTPGM` | 创建程序对象 |
| `CRTSRVPGM` | 创建服务程序 |
| `CRTBNDDIR` | 创建绑定目录 |
| `DSPMOD` | 显示模块信息 |
| `DSPPGM` | 显示程序信息 |
| `DSPSRVPGM` | 显示服务程序 |
| `WRKBNDDIRE` | 管理绑定目录 |

## ILE 与 OPM 对比

| 特性 | OPM（传统） | ILE（现代） |
|------|------------|-------------|
| 混合语言 | 有限 | 完全支持 |
| 过程调用 | 慢（复制） | 快（引用） |
| 内存共享 | 无 | 服务程序共享 |
| 递归 | 不支持 | 支持 |
| 激活组 | 无 | 支持 |
| 事务管理 | 全局 | 组级别 |

## 相关页面

- [[rpg-subprocedures]] — 子过程与函数
- [[service-programs]] — 服务程序详解
- [[ibm-i-platform]] — IBM i 平台
- [[rpg-iv-basics]] — RPG IV 基础
