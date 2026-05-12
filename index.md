---
title: Wiki Index
created: 2026-04-19
updated: 2026-05-12
type: meta
tags: [meta]
---

# Wiki Index

> Map of Content. Every wiki page listed under its topic with a one-line summary.
> Start here to find relevant pages.
> Last updated: 2026-05-12 | Total pages: 75

## RPG Fundamentals

- [[rpg-iv-basics]] — RPG IV 核心规格说明（D/C/F）、指标系统、程序循环、程序结构概述
- [[rpg-iv-bifs]] — 95 个内置函数：字符串/日期/数学/文件/JSON 操作全解析
- [[rpg-iv-data-types]] — 数据类型全览：CHAR/INT/PACKED/DATE/指针/枚举/VARCHAR 等
- [[rpg-free-format]] — 自由格式 vs 固定格式对比、DCL-* 声明、BNDDIR、/FREE /END-FREE
- [[rpg-control-specs]] — 控制规范（Ctl-opt）：程序行为、编译选项、运行时配置
- [[rpg-definition-specs]] — 定义规范（D-spec）：变量、数据结构、原型、接口声明
- [[rpg-file-processing]] — 文件声明、CHAIN/READ/WRITE、INFDS 状态码、文件操作模式
- [[rpg-error-handling]] — 错误处理：状态码/MONITOR/INFSR/%STATUS/异常处理机制
- [[rpg-subprocedures]] — 子过程定义、原型、递归、服务程序绑定、Dcl-proc/End-proc
- [[rpg-parameter-passing]] — 参数传递：CONST/VALUE、*OMIT/*NOPASS、%PARMS、PI 接口
- [[rpg-program-execution]] — 程序运行：CRTBNDRPG、CALL/CL 集成、激活组管理、QCMDEXC
- [[rpg-binding-strategies]] — 三种绑定策略（CRTBNDRPG/OPM/ILE 静态绑定）、BNDDIR、Binder Language
- [[rpg-development-workflow]] — 开发工作流：项目结构、CRTBNDRPG→模块化演进、/COPY 分层

## RPG Advanced

- [[rpg-commitment-control]] — 事务控制：COMMIT/ROLBK、日志、LCKLVL 锁定级别
- [[rpg-xml-json]] — XML/JSON 处理：XML-INTO/XML-SAX/DATA-INTO/DATA-GEN
- [[rpg-storage-heap]] — 动态存储管理：堆/%ALLOC/%REALLOC/链表/动态数组
- [[rpg-java-interop]] — RPG-Java 互调：JNI、JVM 创建、PCML、Native Methods
- [[rpg-debugging]] — 调试：STRDBG、断点/WATCH、DBGVIEW、优化级别
- [[service-programs]] — 服务程序（*SRVPGM）：签名机制、更新策略、BNDSRVPGM

## RPG Opcodes (44 pages)

### Arithmetic & Data Movement
- [[rpg-opcode-adddur]] — 将持续时间加到日期/时间/时间戳
- [[rpg-opcode-cat]] — 字符串拼接（固定格式）
- [[rpg-opcode-eval-corr]] — 对应字段赋值（数据结构间）
- [[rpg-opcode-extrct]] — 从日期/时间/时间戳提取子字段
- [[rpg-opcode-mhlzo]] — 半调整（MHLZO，固定格式）
- [[rpg-opcode-mllzo]] — 长调整（MLLZO，固定格式）
- [[rpg-opcode-move]] — 字符移动赋值（固定格式，右对齐）
- [[rpg-opcode-movea]] — 字符数组移动（固定格式）
- [[rpg-opcode-movel]] — 字符左移动赋值（固定格式，左对齐）
- [[rpg-opcode-mult]] — 乘法（固定格式）
- [[rpg-opcode-mvr]] — 乘法余数（固定格式）
- [[rpg-opcode-SUB]] — 减法（固定格式）
- [[rpg-opcode-SUBDUR]] — 从日期/时间减去持续时间
- [[rpg-opcode-Z-ADD]] — 零加赋值（固定格式）
- [[rpg-opcode-Z-SUB]] — 零减赋值（固定格式）

### Bit Operations
- [[rpg-opcode-bitoff]] — 位关闭（固定格式）
- [[rpg-opcode-biton]] — 位开启（固定格式）
- [[rpg-opcode-TESTB]] — 测试位（固定格式）
- [[rpg-opcode-TESTN]] — 测试数值（固定格式）
- [[rpg-opcode-TESTZ]] — 测试零值（固定格式）
- [[rpg-opcode-TEST]] — 测试（TEST，通用测试操作）

### Comparison & Branching
- [[rpg-opcode-andxx]] — 逻辑与比较（ANDxx 系列）
- [[rpg-opcode-cabxx]] — 比较并分支（CABxx 系列）
- [[rpg-opcode-casxx]] — 条件比较（CASxx 系列）
- [[rpg-opcode-do]] — 循环开始（DO，固定格式）
- [[rpg-opcode-dowxx]] — 当条件为真循环（DOWxx 系列）
- [[rpg-opcode-douxx]] — 直到条件为真循环（DOUxx 系列）
- [[rpg-opcode-dou]] — 直到循环（DOU，固定格式）
- [[rpg-opcode-endyy]] — 结构结束（ENDyy 系列：ENDIF/ENDDO/ENDSL 等）
- [[rpg-opcode-goto]] — 无条件跳转（GOTO/TAG）
- [[rpg-opcode-ifxx]] — 条件判断（IFxx 系列）
- [[rpg-opcode-orxx]] — 逻辑或比较（ORxx 系列）
- [[rpg-opcode-TAG]] — 标签（TAG，配合 GOTO 使用）
- [[rpg-opcode-WHEN-IN]] — WHEN IN 条件判断
- [[rpg-opcode-WHEN-IS]] — WHEN IS 条件判断
- [[rpg-opcode-WHENxx]] — WHEN 条件系列（WHENxx）

### Subroutine & Program Flow
- [[rpg-opcode-begsr]] — 子例程开始/结束（BEGSR/ENDSR）
- [[rpg-opcode-callb]] — 调用绑定子过程（CALLB，固定格式）
- [[rpg-opcode-leavesr]] — 退出子例程（LEAVESR）

### Display & I/O
- [[rpg-opcode-dsply]] — 显示消息（DSPLY，固定格式）
- [[rpg-opcode-out]] — 写输出记录（OUT，固定格式）
- [[rpg-opcode-on-excp]] — 异常输出控制（ON-EXCP）

### XML Processing
- [[rpg-opcode-XML-SAX]] — XML SAX 事件驱动解析

## DDS

- [[dds-physical-files]] — 物理文件：数据类型(P/S/A/H/L/T/Z)、键字段、REF/REFFLD、存储计算
- [[dds-logical-files]] — 逻辑文件：简单 LF/Select-Omit/多格式/JoinLF、JFILE/JFLD/JDFTVAL
- [[dds-keywords]] — DDS 关键字速查：文件级/记录级/字段级关键字、CRTPF/CRTLF 命令
- [[dds-display-files]] — 显示器文件总览：位置条目(1-44)、子文件(SFL)、帮助系统、菜单栏、窗口
- [[dds-display-file-keywords]] — 显示器文件全部 164 个关键字速查（文件级/记录级/字段级/SFL/帮助/菜单）
- [[dds-printer-files]] — 打印机文件：页面布局(LINE/SKIP/SPACE)、字体/条码(COLOR/BARCODE/OVERLAY)
- [[dds-printer-file-keywords]] — 打印机文件全部 65 个关键字速查

## IBM i Platform

- [[ibm-i-platform]] — IBM i 操作系统版本历史、核心组件（ILE/DB2）、对象系统和关键命令
- [[ile-concepts]] — ILE 架构：模块/服务程序/激活组/绑定目录
- [[ibm-i-api-concepts]] — API 核心概念：API 类型（program/service/UNIX）、调用方式、User Space、Error Code
- [[ibm-i-api-reference]] — API 分类速查：User Space/List/Object/Job/Message/Exit Program API 一览
- [[ibm-i-user-space]] — User Space 机制与 List API：指针/非指针两种访问模式、QUSL0100 通用列表头

## Comparisons

- [[whats-new-7-5]] — IBM i 7.5 vs 7.3/7.4 RPG 新特性对比：%SPLIT/枚举/CHARCOUNT

## Raw Sources

- [[raw/papers/rpg-reference-7.5.txt]] — IBM ILE RPG Reference for IBM i 7.5（SC09-2508-12，1104 页）
- [[raw/papers/rpg-programmers-guide-7.5.txt]] — IBM ILE RPG Programmer's Guide for IBM i 7.5（SC09-2507-12，562 页）
- [[raw/papers/dds-physical-logical-7.5.txt]] — DDS for Physical and Logical Files（RZAKB，102 页，v7.2）
- [[raw/papers/dds-display-files.txt]] — DDS for Display Files（RZAKC，288 页，v7.2）
- [[raw/papers/dds-printer-files.txt]] — DDS for Printer Files（RZAKD，160 页，v7.2）
- [[raw/papers/apipdf.txt]] — IBM i 7.5 API Overview and Concepts（apipdf.pdf，476 页，v7.2）
