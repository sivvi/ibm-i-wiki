---
title: ILE RPG 绑定策略
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, ile, compilation]
sources: [raw/papers/rpg-programmers-guide-7.5.txt, raw/papers/rpg-reference-7.5.txt]
---

# ILE RPG 绑定策略

ILE 支持两种绑定（Binding）方式将过程链接到程序：**静态绑定**（通过 CRTPGM/CRTSRVPGM）和**动态绑定**（CRTBNDRPG）。

## 三种构建策略

### Strategy 1: CRTBNDRPG（一步到位）

适用于单模块原型开发，最快但不支持多模块/服务程序。

```bash
CRTBNDRPG PGM(MYLIB/MYPROG) SRCFILE(MYLIB/QRPGLESRC) SRCMBR(MYPGM)
```

生成一个可运行的 `*PGM` 对象，包含编译后的代码。

### Strategy 2: OPM 兼容模式（CRTPGM + OPM）

传统 OPM 程序的构建方式，不使用 ILE 特性。

```bash
CRTPGM PGM(MYLIB/OPMPROG) MODULE(MYLIB/RPGMOD) DFTACTGRP(*YES)
```

### Strategy 3: ILE 静态绑定（推荐生产用法）

```
源文件 → CRTRPGMOD → 模块(*MODULE)
                              ↓
                    CRTSRVPGM → 服务程序(*SRVPGM)
                              ↓
                    CRTPGM + BNDDIR/BNDSRVPGM → 程序(*PGM)
```

**单语言场景：**
```bash
# 1. 编译模块
CRTRPGMOD MODULE(MYLIB/HELLOMOD) SRCFILE(MYLIB/QRPGLESRC)

# 2. 绑定模块到程序
CRTPGM PGM(MYLIB/HELLOMOD) MODULE(MYLIB/HELLOMOD)
```

**混合语言场景（RPG + C）：**
```bash
# RPG 模块
CRTRPGMOD MODULE(MYLIB/RPGMOD) SRCFILE(MYLIB/QRPGLESRC)

# C 模块
CRTCMOD MODULE(MYLIB/CMOD) SRCFILE(MYLIB/QCSRC') LNGPKG(*PGM)

# 绑定为程序
CRTPGM PGM(MYLIB/MIXEDAPP) MODULE(MYLIB/RPGMOD MYLIB/CMOD)
```

## 绑定目录（Binding Directory）

绑定目录是预编译的对象列表，相当于"搜索路径"，避免每次指定 MODULE：

```bash
CRTBNDDIR BNDDIR(MYLIBS/UTILS)
ADDBNDDIRE BNDDIR(MYLIBS/UTILS) OBJ(MYLIB/STRINGS MYLIB/DATES)
```

```rpgle
**FREE
Ctl-opt BNDDIR('MYLIBS/UTILS');
// 现在 STRINGS 和 DATES 模块中的过程可直接调用
```

## 绑定服务程序（BNDSRVPGM）

直接绑定已存在的服务程序：

```bash
CRTPGM PGM(MYLIB/MYAPP) MODULE(MYLIB/MAINMOD) +
      BNDSRVPGM(MYLIB/UTILSRV MYLIB/LOGSRV)
```

## 绑定语言（Binder Language）

Binder Language 是服务程序中定义导出接口的 DSL，用于 `*SRVPGM` 的 `EXPORT(*SRCFILE)` 方式：

```rpgle
*================================================================*
*  Binder Language source for service program UTILSRV         *
*================================================================*
STRPGMEXP PGMLVL(*CURRENT) SIGNATURE('UTILSRV_1')
  EXPORT SYMBOL('toUpper')
  EXPORT SYMBOL('toLower')
  EXPORT SYMBOL('trim')
ENDPGMEXP
```

### 签名（Signature）

签名是服务程序接口的版本标识：

- **签名兼容**：新增导出（不删除/不改参数）→ 老程序仍可用
- **签名不兼容**：删除/改变导出 → 老程序报错

```bash
# 查看签名
DSPPGM PGM(MYLIB/MYPROG) DETAIL(*SIGNATURE)
```

### 创建服务程序

```bash
# Step 1: 编译各模块
CRTRPGMOD MODULE(MYLIB/UTIL1) SRCFILE(MYLIB/QRPGLESRC)
CRTRPGMOD MODULE(MYLIB/UTIL2) SRCFILE(MYLIB/QRPGLESRC)

# Step 2: 创建绑定目录（管理模块列表）
CRTBNDDIR BNDDIR(MYLIB/UTILBND)
ADDBNDDIRE BNDDIR(MYLIB/UTILBND) MODULE(MYLIB/UTIL1 MYLIB/UTIL2)

# Step 3: 创建服务程序
CRTSRVPGM SRVPGM(MYLIB/UTILSRV) BNDDIR(MYLIB/UTILBND)
```

## 相关命令速查

| 命令 | 用途 |
|------|------|
| `CRTBNDRPG` | 单步编译运行（开发用） |
| `CRTRPGMOD` | 编译为模块 |
| `CRTPGM` | 绑定模块/服务程序为程序 |
| `CRTSRVPGM` | 创建服务程序 |
| `CRTBNDDIR` | 创建绑定目录 |
| `UPDPGM` | 更新程序绑定（不重编） |
| `RMVBNDIRE` | 从绑定目录移除 |
| `DSPMOD` | 查看模块详情 |

## 相关页面

- [[ile-concepts]] — ILE 架构
- [[service-programs]] — 服务程序详解
- [[rpg-subprocedures]] — 子过程与原型
- [[rpg-debugging]] — 调试
