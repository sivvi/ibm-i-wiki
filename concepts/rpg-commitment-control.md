---
title: RPG Commitment Control
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, database, transaction, commitment-control]
sources: [raw/papers/rpg-programmers-guide-7.5.txt]
related:
  - rpg-file-processing
  - rpg-error-handling
  - rpg-iv-bifs
---

# RPG Commitment Control（事务控制）

Commitment Control（承诺控制，简称 CMTC）是 IBM i 上用于将多个文件 I/O 操作打包为原子事务的机制。要么全部提交（COMMIT），要么全部回滚（ROLBK）。常用于金融、库存等需要强一致性的业务场景。

## 核心概念

### 事务语义

| 操作 | 说明 |
|------|------|
| **COMMIT** | 确认（永久化）所有自上一 COMMIT/ROLBK 以来的更改 |
| **ROLBK** | 回滚所有更改，恢复到上一个 COMMIT 点 |
| **隐式 ROLBK** | 系统故障时自动触发，等同于 ROLBK |

### 与 RPG 程序周期的兼容性

> **重要**：Commitment Control **仅适用于全流程文件（full procedural files）**，即 I/O 操作由程序逻辑控制。**不可用于主/次文件（primary/secondary files）**，因为程序周期会干扰事务边界。

原因：
- 无法为最后一次 total output 发出 COMMIT
- 锁定记录后的恢复逻辑难以在周期内编程
- ROLBK 不会重置 Level Indicator
- ROLBK 后处理匹配记录可能产生序列错误

## CL 命令准备

在使用前需创建日志相关对象：

```cl
// 1. 创建日志接收器
CRTJRNRCV JRNRCV(RECEIVER)

// 2. 创建日志
CRTJRN JRN(JOURNAL) JRNRCV(RECEIVER)

// 3. 将文件加入日志
STRJRNPF FILE(MASTER TRANS) JRN(JOURNAL)

// 4. 启动 commitment control
STRCMTCTL LCKLVL(*ALL)

// 5. 运行程序
CALL REVISE

// 6. 结束 commitment control（自动 ROLBK 未提交事务）
ENDCMTCTL
```

## 锁定级别（LCKLVL）

| 级别 | 说明 |
|------|------|
| `LCKLVL(*ALL)` | 最高锁定——其他程序完全不能读取或更新已处理记录 |
| `LCKLVL(*CHG)` | 其他使用 commitment control 的程序可读取，但不可更新 |
| `LCKLVL(*CS)` | 仅读锁（cursor stability） |

### 锁定行为详解

当程序在 commitment control 下处理过某记录后：
- **本程序**：可访问
- **同一激活组/作业中，有 commitment control 的程序**：可读（共享文件也可更新）
- **同一激活组/作业中，无 commitment control 的程序**：不可读写
- **其他作业中有 commitment control 的程序**：`*ALL` 下不可读，`*CHG` 下可读
- **其他作业中无 commitment control 的程序**：可读不可更新

> 锁定只能由 COMMIT/ROLBK 释放，UNLOCK 命令**无法**释放 commitment control 锁。

## 文件声明

```rpgle
FMASTER    UF   E      K      DISK    COMMIT
FTRANS     UF   E      K      DISK    COMMIT
```

`COMMIT` 关键字声明该文件受 commitment control 管理。

## RPG 操作码

### COMMIT

```rpgle
COMMIT label_name
```

- `factor1`：标识符（用于异常恢复时识别最后一个成功提交的事务组）
- 发布后释放所有受控记录的锁，将更改永久写入数据库

### ROLBK

```rpgle
ROLBK
```

- 无参数
- 撤销当前事务内所有更改，恢复到上一个 COMMIT 点

## 基本示例

```rpgle
FMASTER    UF   E      K      DISK    COMMIT
FTRANS     UF   E      K      DISK    COMMIT
...
 // 使用 COMMIT 完成一组操作，或 ROLBK 回滚
 C                   UPDATE    MAST_REC                             90
 C                   UPDATE    TRAN_REC                             91
 C                   IF        *IN90 OR *IN91
 C                   ROLBK
 C                   ELSE
 C                   COMMIT
 C                   ENDIF
```

## 条件式 Commitment Control

运行时才决定是否启用 commitment control，避免维护两套程序：

```rpgle
 // COMMIT 关键字带参数
 FMASTER    UF   E      K      DISK    COMMIT(COMITFLAG)
 FTRANS     UF   E      K      DISK    COMMIT(COMITFLAG)

 C     *ENTRY        PLIST
 C                   PARM                    COMITFLAG

 C                   UPDATE    MAST_REC                             90
 C                   UPDATE    TRAN_REC                             91
 C                   IF        COMITFLAG = '1'
 C                   IF        *IN90 OR *IN91
 C                   ROLBK
 C                   ELSE
 C                   COMMIT
 C                   ENDIF
 C                   ENDIF
```

调用时设置标志位：`COMITFLAG = '1'` 启用，否则禁用。

## Commitment Definition 作用域

启动 STRCMTCTL 后，系统创建 commitment definition，其作用域：

| 作用域 | 说明 |
|--------|------|
| **激活组级别**（默认） | 仅同一激活组内的程序共享同一 commitment definition |
| **作业级别** | 整个作业内所有程序共享 |

可通过 `CMTSCOPE` 参数控制。

## OPEN/CLOSE 与 COMMIT/ROLBK 的关系

- OPEN/CLOSE **不影响** COMMIT/ROLBK 的行为
- COMMIT/ROLBK **跨文件关闭**：在步骤 3 关闭文件后，步骤 5 的 ROLBK 仍会回滚步骤 3 的更改
- 激活组/作业结束时，ENDCMTCTL 会自动发出隐式 ROLBK

## 与 SETLL/SETGT 的锁定关系

> SETLL 和 SETGT（不带更新）会像普通读操作一样锁定记录——这一点在 commitment control 下同样适用。

## 相关页面

- [[rpg-file-processing]] — 文件处理基础
- [[rpg-error-handling]] — 错误处理
- [[%EOF]] — %EOF 等文件状态 BIF
