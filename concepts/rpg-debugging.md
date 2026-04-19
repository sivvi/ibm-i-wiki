---
title: RPG 程序调试
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, debugging]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
---

# RPG 程序调试

## 调试工具

IBM i 提供的调试工具：

| 工具 | 命令 | 说明 |
|------|------|------|
| ILE Source Debugger | `STRDBG` | 语句级/断点调试 |
| System Debugger | `STRDBG` | 传统 OPM 调试 |
| Module Debug View | `DSPMOD` | 查看模块信息 |

## 创建可调试的程序

调试需要在编译时启用调试视图：

```bash
# 编译时启用调试
CRTRPGMOD MODULE(MYLIB/MYPGM) SRCFILE(MYLIB/QRPGLESRC) DBGVIEW(*LIST)

# 或用 CRTBNDRPG
CRTBNDRPG PGM(MYLIB/MYPGM) SRCFILE(MYLIB/QRPGLESRC) DBGVIEW(*LIST)
```

`DBGVIEW` 选项：
- `*NONE`：不生成调试信息
- `*LIST`：生成语句列表视图（**推荐生产**）
- `*STMT`：生成语句号
- `*ALL`：所有视图（**开发用**）
- `*SRCDBG`：源级调试

## 启动调试会话

```bash
# 调试 ILE 程序
STRDBG PGM(MYLIB/MYPGM) UPDPROD(*YES)

# 调试 OPM 程序
STRDBG PGM(MYLIB/OPMPGM)
```

## 断点类型

### 语句断点

```rpgle
// 在需要调试的模块中：
// 断点会在该语句执行前停下
total = base + tax;  // ← 在此设断点
```

### 条件断点

```
BREAK PGM(MyLib/MYPGM) MODULE(MYMOD) STMT(50) COND('counter > 100')
```

### 监视条件（WATCH）

```bash
# 监视变量变化
WATCH VAR(MyLib/MYPGM counter) COND('counter > 100')

# 或在调试器中
ADD_WATCH module(MyMod) var(counter) cond('counter > 100')
```

## 调试命令

在调试器中输入：

| 命令 | 缩写 | 说明 |
|------|------|------|
| `STEP` | `S` | 单步执行（进入 CALL） |
| `STEP IN` | `F` | 单步执行（跳过 CALL） |
| `STEP OVER` | `T` | 单步到下一语句 |
| `BREAK` | `B` | 设置断点 |
| `CLEAR` | `CL` | 清除断点 |
| `PRINT` | `P` | 打印变量值 |
| `EVAL` | `E` | 求值表达式 |
| `CALL` | `C` | 调用程序 |
| `RET` | `R` | 从过程返回 |
| `WATCH` | `W` | 设置监视条件 |
| `DISPLAY` | `D` | 显示内存/变量 |
| `QUAL` | `Q` | 进入限定结构 |

## 调试示例

```rpgle
**FREE
// 源文件: MYLIB/QRPGLESRC(MYCALC)
DCL-S base PACKED(9:2) INZ(100);
DCL-S tax PACKED(9:2) INZ(8);
DCL-S total PACKED(9:2);

total = base + tax;       // 设断点在此
*INLR = *ON;
```

调试过程：

```
> STRDBG PGM(MYLIB/MYCALC) UPDPROD(*YES)

> BREAK MODULE(MYCALC) STMT(8)
Breakpoint set at statement 8 in MYLIB/MYCALC

> CALL MYCALC
Breakpoint hit at statement 8

DBG> PRINT total
TOTAL = 0

DBG> STEP
Statement 9, stopped before *INLR

DBG> PRINT total
TOTAL = 108.00

DBG> RET
Program MYCALC ended.
```

## 调试服务程序

```bash
# Step 1: 确保模块编译时带了调试视图
CRTRPGMOD MODULE(MYLIB/UTILSRV) SRCFILE(MYLIB/QRPGLESRC) DBGVIEW(*LIST)

# Step 2: 重建服务程序
CRTSRVPGM SRVPGM(MYLIB/UTILSRV) MODULE(*SRVPGM) +
          EXPORT(*SRCFILE) SRCFILE(MYLIB/QSRVSRC) SRCMBR(UTILSRV)

# Step 3: 调试调用程序
STRDBG PGM(MYLIB/MYAPP) UPDPROD(*YES)

# Step 4: 在服务程序过程中设断点
BREAK UTILSRV/toUpper
```

## 调试激活组

```bash
# 调试特定激活组
STRDBG PGM(MYLIB/MYPGM) ACTGRP(MYACTGRP)
```

## 常见问题

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 看不到源 | 编译时没开 DBGVIEW | 重新编译 `DBGVIEW(*LIST)` |
| 断点无效 | 优化级别太高 | 编译用 `OPTIMIZE(*NONE)` |
| 变量值不对 | 优化导致变量被消除 | `EVAL` 直接看内存 |
| MCH06042 | 空指针 | 检查 `%ADDR` 赋值 |

## 编译选项速查

```bash
# 开发编译（完全可调试）
CRTRPGMOD MODULE(MYLIB/DEVPGM) SRCFILE(MYLIB/QRPGLESRC) +
     DBGVIEW(*ALL) OPTIMIZE(*NONE)

# 生产编译（优化）
CRTRPGMOD MODULE(MYLIB/RELPGM) SRCFILE(MYLIB/QRPGLESRC) +
     DBGVIEW(*STMT) OPTIMIZE(*FULL)
```

## 相关页面

- [[ile-concepts]] — ILE 架构
- [[rpg-binding-strategies]] — 绑定策略
- [[service-programs]] — 服务程序
