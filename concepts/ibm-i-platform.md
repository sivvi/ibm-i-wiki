---
title: IBM i 平台
created: 2026-04-19
updated: 2026-04-19
type: entity
tags: [ibm-i, i-series]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# IBM i 平台

IBM i 是 IBM 发布的集成操作系统，最初为 AS/400（Application System/400）设计，后更名为 iSeries 和 IBM i。

## 版本历史

| 版本 | 发布年份 | 主要特性 |
|------|---------|---------|
| V3R7 | 1994 | RPG IV 引入 |
| V4R2 | 1996 | ILE 引入 |
| V5R1 | 2001 | 自由格式 RPG |
| V5R4 | 2006 | UTF-8 支持 |
| V6R1 | 2010 | 64 位支持 |
| V7R1 | 2012 | 枚举类型 |
| V7R2 | 2014 | 更强 BIFs |
| V7R3 | 2016 | JSON BIFs |
| V7R4 | 2019 | 改进指针 |
| V7R5 | 2022 | 持续增强 |
| 7.5 | 2023 | 最新稳定版 |

## 核心组件

### 集成语言环境（ILE）

IBM i 的现代编程模型，支持多语言混合开发（RPG、C、COBOL、CL）。

参见：[[ile-concepts]]

### 对象系统

IBM i 使用单级存储（Single-Level Storage）和对象导向架构：

```
库（*LIB）─ 程序（*PGM）
           ├─ 模块（*MODULE）
           ├─ 服务程序（*SRVPGM）
           ├─ 文件（*FILE）
           │   ├─ 物理文件（*PF）
           │   └─ 逻辑文件（*LF）
           ├─ 数据区域（*DTAARA）
           └─ 数据队列（*QMQRY）
```

### 数据库（DB2 for i）

IBM i 内置的 SQL 数据库：

```sql
CREATE TABLE MYLIB/CUSTOMER (
    CUSTNO CHAR(10) PRIMARY KEY,
    NAME CHAR(50),
    BALANCE DEC(15,2)
);
```

### 许可程序（Licensed Programs）

| 产品号 | 名称 | 用途 |
|--------|------|------|
| 5770-WDS | Rational Development Studio for i | RPG/COBOL/C 编译器 |
| 5770-ST1 | IBM i Access | ODBC/JDBC/CLI |
| 5770-XW1 | XML Toolkit | XML 解析 |

## 关键命令

| 命令 | 用途 |
|------|------|
| `WRKMBRPDM` | 库成员工作台 |
| `CRTRPGMOD` | 编译 RPG 模块 |
| `CRTPGM` | 创建程序 |
| `CRTSQLRPG` | 编译 SQL RPG |
| `DSPOBJD` | 显示对象详情 |
| `WRKOBJLCK` | 检查对象锁 |
| `DSPSPLF` | 查看作业输出 |

## 相关页面

- [[ile-concepts]] — ILE 架构
- [[rpg-iv-basics]] — RPG IV 基础
- [[rpg-iv-bifs]] — 内置函数
- [[service-programs]] — 服务程序
