---
title: RPG IV 基础
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, concepts]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG IV 基础

RPG（Report Program Generator）是一种业务编程语言，最初于 1959 年在 IBM 1620 上开发。RPG IV（也称为 RPG ILE）是 IBM i 平台上的现代版本，相比早期版本有了根本性的改变。

## 核心规格说明（Specifications）

RPG IV 使用六种主要规格说明：

| 规格 | 用途 | 位置 |
|------|------|------|
| **D** (Definition) | 定义变量、数据结构、子过程接口 | 全列 |
| **C** (Calculation) | 计算和程序逻辑 | 全列 |
| **Dcl-s/FC | Free-form 变量声明 | 行内 |
| **Dcl-ds/End-ds | Free-form 数据结构 | 行内 |
| **Dcl-proc/End-proc | Free-form 子过程 | 行内 |
| **Ctl-opt | 控制选项 | 行内 |

## 文件处理

RPG IV 有三种文件处理方式：

1. **外部描述文件（Externally Described）**：文件结构由 DDS 定义，RPG 程序直接使用字段名
2. **程序描述文件（Program Described）**：文件结构在程序中用 F-spec 定义
3. **LF（Logical File）**：视图或索引，可 Join 多个 PF

## 指标（Indicators）

RPG 使用指标（3 字符标识，如 *IN03、*INLR）来处理条件逻辑：

- **LR**（Last Record）：请求正常结束
- **RT**（Return）：请求退回
- **1P**：首页处理
- **KA-KN**：功能键指标
- **OA-OG, OV**：溢出指标

## 关键内置函数（BIFs）

```rpgle
DCL-S value INT(10);
DCL-S result CHAR(20);
DCL-S isOpen IND;

value = %INT('42');          // 字符串转整数
result = %CHAR(value);       // 整数转字符
isOpen = %OPEN(MyFile);      // 检查文件是否打开
```

## 程序结构示例

```rpgle
**FREE
// 最小 RPG IV 程序
Dcl-s Message Char(50);
Message = 'Hello, IBM i';
Dsply Message;
*INLR = *ON;
```

## 相关页面

- [[rpg-iv-bifs]] — 内置函数详解
- [[rpg-iv-data-types]] — 数据类型
- [[rpg-free-format]] — Free format RPG
- [[ile-concepts]] — ILE 架构
- [[ibm-i-platform]] — IBM i 平台
