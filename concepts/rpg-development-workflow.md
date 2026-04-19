---
title: RPG 开发工作流
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, best-practices, ile]
sources: [raw/papers/rpg-programmers-guide-7.5.txt, raw/papers/rpg-reference-7.5.txt]
---

# RPG 开发工作流

## 典型项目结构

```
MYLIB
 ├─ QRPGLESRC    (RPG 源文件)
 ├─ QSRVSRC      (Binder Language 源)
 ├─ QCLSRC       (CL 源文件)
 ├─ QDDSSRC      (DDS 源文件)
 ├─ QCBLSRC      (COBOL 源文件)
 ├─ *MODULE      (编译产物)
 ├─ *SRVPGM      (服务程序)
 └─ *PGM         (可执行程序)
```

## 开发阶段

### Phase 1: 原型开发（CRTBNDRPG）

适用于快速验证想法、一次性脚本：

```bash
CRTBNDRPG PGM(MYLIB/PROTO1)
     SRCFILE(MYLIB/QRPGLESRC)
     SRCMBR(PROTO1)
     DFTACTGRP(*NO)
     ACTGRP(*NEW)
```

### Phase 2: 模块化开发（CRTRPGMOD）

当代码需要复用、拆分成多个过程时：

```
开发流程:
1. 写模块源代码 (QRPGLESRC)
2. CRTRPGMOD → *.MODULE
3. 写 Binder Language (QSRVSRC)
4. CRTSRVPGM → *.SRVPGM
5. CRTPGM → *.PGM
```

### Phase 3: 服务程序架构（生产）

```
shared/
  ├─ util-srv      (常用工具服务程序)
  ├─ str-srv       (字符串处理)
  └─ date-srv      (日期计算)

app/
  ├─ main-pgm      (主程序)
  ├─ main-mod      (主模块)
  └─ app-srv       (应用专用服务程序)
```

## 源代码组织

### 分层架构

```rpgle
**FREE
// ===== 主程序层 =====
Ctl-opt DFTACTGRP(*NO) ACTGRP('MYAPP');

// 引入共享服务程序的原型
/copy mylib/qcpysrc,util_h       // 原型声明

DCL-S result VARCHAR(100);

// 调用服务程序
result = toUpper('hello world');
*INLR = *ON;
```

### Copy 文件（/COPY）

```rpgle
**FREE
// util_h.rpgle — 原型头文件
DCL-PR toUpper VARCHAR(256) END-PR;
DCL-PR toLower VARCHAR(256) END-PR;
DCL-PR trim VARCHAR(256) END-PR;
```

在源文件中使用：

```rpgle
**FREE
/copy mylib/qcpysrc,util_h

DCL-S msg VARCHAR(256);
msg = toUpper('hello');
```

### /INCLUDE vs /COPY

| 指令 | 说明 |
|------|------|
| `/COPY` | 编译时展开，常用 |
| `/INCLUDE` | 同 /COPY，语义等价 |
| 条件编译 `/COPY` | 配合 `/IF`/`/ENDIF` 选择性包含 |

## SQL 集成工作流

```bash
# 直接编译含嵌入式 SQL 的 RPG
CRTSQLRPG PGM(MYLIB/SQLAPP)
     SRCFILE(MYLIB/QRPGLESRC)
     SRCMBR(SQLAPP)
     OBJTYPE(*PGM)
     COMMIT(*NONE)
```

```rpgle
**FREE
DCL-DS customer_t QUALIFIED;
    id CHAR(10);
    name CHAR(50);
    balance PACKED(15:2);
END-DS;

EXEC SQL SELECT id, name, balance
    INTO :customer_t.id, :customer_t.name, :customer_t.balance
    FROM customer WHERE id = :inputId;
```

## 版本与发布

### 目标版本（TGTRLS）

```bash
# 编译为兼容 V7R4M0
CRTRPGMOD MODULE(MYLIB/MYMOD)
     SRCFILE(MYLIB/QRPGLESRC)
     TGTRLS(V7R4M0)
```

### 迁移检查

```bash
# 查看 RPG 版本
CRTRPGMOD ... OPTION(*EXT) OUTPUT(*PRINT)
```

## 性能优化

| 优化项 | 方法 |
|--------|------|
| 编译优化 | `OPTIMIZE(*FULL)` |
| 减少文件 I/O | 批量读入数组，内存处理 |
| 减少字符串复制 | 用 `CONST` 参数 |
| 服务程序共享 | 多模块合并到同一 SRVPGM |
| 避免 OPM 绑定 | 全部用 ILE |

## 常见目录结构

```
库列表顺序:
*LIBL (系统库)
  ├─ QGPL          (通用程序库)
  ├─ MYDEVL        (开发库 — 源码)
  └─ MYPRODL       (生产库 — 对象)
```

开发时 `*LIBL` 指向 `MYDEVL`，上线时指向 `MYPRODL`。

## 代码规范

| 规范 | 示例 |
|------|------|
| 文件命名 | `MYLIB/QRPGLESRC(MYPROC)` |
| 变量命名 | `Dcl-s customerName CHAR(50);` |
| 常量命名 | `DCL-C MAX_RETRIES 3;` |
| 过程命名 | `DCL-PROC calculateTotal;` |
| 数据结构 | `DCL-DS customer_t QUALIFIED;` |
| 前缀 | `cust_` 前缀区分字段 |

## 相关页面

- [[rpg-binding-strategies]] — 绑定策略
- [[service-programs]] — 服务程序
- [[rpg-debugging]] — 调试
- [[ile-concepts]] — ILE 架构
- [[rpg-subprocedures]] — 子过程
- [[rpg-iv-bifs]] — 内置函数
- [[ibm-i-platform]] — IBM i 平台
