---
title: Wiki Schema
created: 2026-04-19
updated: 2026-04-19
type: meta
tags: [meta]
---

# Wiki Schema — IBM iSeries RPG 语言知识库

## Domain
IBM iSeries (AS/400) 平台上的 RPG 编程语言，包括 RPG ILE、RPG IV、RPG III 等版本，以及 IBM i 操作系统的相关知识。

## Conventions
- 文件名：小写、连字符、无空格（如 `rpg-iv-basics.md`）
- 每个 wiki 页面以 YAML frontmatter 开头
- 使用 `[[wikilinks]]` 链接其他页面（每页至少 2 个出站链接）
- 更新页面时更新 `updated` 日期
- 新页面必须添加到 `index.md` 的对应 section
- 所有操作必须 append 到 `log.md`

## Tag Taxonomy（标签分类）

### 语言与技术
- `rpg` — RPG 语言基础
- `rpg-iv` — RPG IV（ILE）特性
- `rpg-free` — RPG Free 格式
- `built-in-functions` — 内置函数（BIF）
- `data-types` — 数据类型
- `file-processing` — 文件处理
- `subprocedures` — 子过程与函数
- `error-handling` — 错误处理

### IBM i 平台
- `ibm-i` — IBM i 操作系统
- `i-series` — iSeries/AS/400 硬件平台
- `ile` — 集成语言环境
- `service-programs` — 服务程序
- `modules` — 模块

### 工具与调试
- `debugging` — 调试技巧
- `tools` — 开发工具
- `compilation` — 编译与构建

### 概念与模式
- `best-practices` — 最佳实践
- `patterns` — 设计模式
- `migration` — 版本迁移（RPG III → RPG IV 等）

## Page Thresholds（页面创建规则）
- **创建页面**：某概念/特性在 2+ 来源中出现，或在单一来源中是核心内容
- **追加到现有页**：来源提到了已覆盖的内容
- **不创建页面**：仅一次提及的次要细节
- **拆分页面**：超过 200 行时拆分子主题

## Entity Pages
- IBM iSeries 服务器型号
- 主要 IBM i 版本（7.1, 7.2, 7.3, 7.4, 7.5）
- RPG 编译器版本
- 主要 DDS 文件类型

## Concept Pages
- RPG 语言版本演进
- ILE 架构
- 文件处理模型
- 指针与内存管理
- 程序调用机制

## Update Policy
新旧信息冲突时：
1. 以较新的来源为准
2. 确有矛盾时，两说并存，标注日期和来源
3. 在 frontmatter 标记 `contradictions: [page-name]`

## Log Rotation
当 `log.md` 超过 500 条时，轮换为 `log-YYYY.md`
