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

### 指标类型

| 范围 | 用途 |
|------|------|
| 01-99 | 普通指标 |
| L1-L9 | 控制级别指标 |
| LR | 最后记录指标 |
| RT | 返回指标 |
| 1P | 首页处理指标 |
| KA-KN, KP-KY | 功能键指标 |
| OA-OG, OV | 溢出指标 |
| U1-U8 | 外部指标 |
| H1-H9 | 停止指标 |
| MR | 匹配记录指标 |

### 控制级别指标（L1-L9）

L1-L9 用于控制字段改变时触发总计计算。当控制字段内容改变时，指定级别的指标会被设置为 ON，触发相应的总计处理。

```
// 输入规格中的控制字段定义
I..................................L1.....M1
I CustNo    1    5
I OrderNo   6   12
I Amount   13   20P
```

当 CustNo 改变时，L1 指标打开，触发 L1 级别的总计计算。

### 溢出指标（OA-OG, OV）

溢出指标用于打印文件，当页面溢出时触发。

### RPG 程序循环（RPG Cycle）

RPG IV 程序循环是传统 RPG 的核心执行模型：

1. **初始化**：程序启动时的初始处理
2. **读记录**：从主文件读取下一条记录
3. **详细计算**：对每条记录执行计算
4. **总计计算**：控制级别改变时执行
5. **输出**：生成输出
6. **循环**：返回步骤 2，直到文件结束
7. **结束**：LR 指标打开时执行最后的总计和输出

```
 *INLR = *ON;  // 关闭 RPG 循环
```

对于现代 RPG IV 程序，建议使用:
- `MAIN()` 或 `NOMAIN` 关键字
- 完全过程化的文件处理（CHAIN, READ, etc.）
- ILE 程序和模块概念

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
