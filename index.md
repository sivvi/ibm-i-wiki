# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-04-19 | Total pages: 29

## Entities

- [[ibm-i-platform]] — IBM i 操作系统版本历史、核心组件（ILE/DB2）和关键命令

## Concepts

- [[rpg-iv-basics]] — RPG IV 核心规格说明（D/C/F）、指标系统、程序结构概述
- [[rpg-iv-bifs]] — 50+ 内置函数：字符串/日期/文件/JSON 操作全解析
- [[rpg-iv-data-types]] — 数据类型全览：CHAR/INT/PACKED/DATE/指针/枚举等
- [[rpg-free-format]] — 自由格式 vs 固定格式对比、DCL-* 声明、BNDDIR 等
- [[rpg-subprocedures]] — 子过程定义、原型、递归、服务程序（SRVPGM）
- [[rpg-file-processing]] — 文件声明、CHAIN/READ/WRITE、INFDS 状态码
- [[rpg-error-handling]] — 错误处理（状态码/MONITOR/INFSR/%STATUS/异常）
- [[rpg-commitment-control]] — Commitment Control 事务控制：COMMIT/ROLBK、日志、LCKLVL 锁定级别 [[rpg-file-processing]]
- [[rpg-xml-json]] — XML/JSON 处理：XML-INTO/XML-SAX/DATA-INTO/DATA-GEN [[rpg-iv-bifs]]
- [[rpg-storage-heap]] — 动态存储管理：堆/%ALLOC/%REALLOC/链表/动态数组 [[rpg-iv-data-types]]
- [[rpg-binding-strategies]] — 三种绑定策略（CRTBNDRPG/OPM/ILE静态绑定）、BNDDIR、Binder Language
- [[service-programs]] — 服务程序（*SRVPGM）：签名机制、更新策略、BNDSRVPGM
- [[rpg-parameter-passing]] — 参数传递：CONST/VALUE、*OMIT/*NOPASS、%PARMS、PI
- [[rpg-java-interop]] — RPG-Java 互调：JNI、JVM 创建、PCML、Native Methods
- [[rpg-debugging]] — 调试：STRDBG、断点/WATCH、DBGVIEW、优化级别
- [[rpg-program-execution]] — 程序运行：CRTBNDRPG、CALL/CL集成、激活组管理、QCMDEXC
- [[rpg-development-workflow]] — 开发工作流：项目结构、CRTBNDRPG→模块化演进、/COPY分层
- [[dds-physical-files]] — DDS 物理文件：数据类型(P/S/A/H/L/T/Z)、键字段、REF/REFFLD、存储计算
- [[dds-logical-files]] — DDS 逻辑文件：简单LF/Select-Omit/多格式/JoinLF、JFILE/JFLD/JDFTVAL
- [[dds-keywords]] — DDS 关键字速查：文件级/记录级/字段级关键字、CRTPF/CRTLF 命令
- [[dds-display-files]] — DDS 显示器文件总览：位置条目(1-44)、子文件(SFL)、帮助系统、菜单栏、窗口 [[dds-display-file-keywords]]
- [[dds-display-file-keywords]] — 显示器文件全部164个关键字速查（文件级/记录级/字段级/SFL/帮助/菜单）
- [[dds-printer-files]] — DDS 打印机文件：页面布局(LINE/SKIP/SPACE)、字体/条码(COLOR/BARCODE/OVERLAY) [[dds-printer-file-keywords]]
- [[dds-printer-file-keywords]] — 打印机文件全部65个关键字速查
- [[ile-concepts]] — ILE 架构：模块/服务程序/激活组/绑定目录
- [[ibm-i-api-concepts]] — IBM i API 核心概念：API类型（program/service/UNIX）、调用方式（ILE C/OPM RPG/COBOL）、User Space、Error Code
- [[ibm-i-api-reference]] — IBM i API 分类速查：User Space/List/Object/Job/Message/Exit Program API 一览 [[ibm-i-user-space]]
- [[ibm-i-user-space]] — User Space 机制与 List API：指针/非指针两种访问模式、QUSL0100 通用列表头

## Comparisons

- [[whats-new-7-5]] — IBM i 7.5 vs 7.3/7.4 RPG 新特性对比，%SPLIT/枚举/CHARCOUNT

## Raw Sources

- [[raw/papers/rpg-reference-7.5.txt]] — IBM ILE RPG Reference for IBM i 7.5（SC09-2508-12，1104 页）
- [[raw/papers/rpg-programmers-guide-7.5.txt]] — IBM ILE RPG Programmer's Guide for IBM i 7.5（SC09-2507-12，562 页）
- [[raw/papers/dds-physical-logical-7.5.txt]] — DDS for Physical and Logical Files（RZAKB，102 页，7.2版）
- [[raw/papers/dds-display-files.txt]] — DDS for Display Files（RZAKC，288 页，7.2版）
- [[raw/papers/dds-printer-files.txt]] — DDS for Printer Files（RZAKD，160 页，7.2版）
- [[raw/papers/apipdf.txt]] — IBM i 7.5 API Overview and Concepts（apipdf.pdf，476 页，v7.2版）
