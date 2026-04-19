---
title: IBM i API Reference
type: concept
domain: ibm-i
tags: [ibm-i, api, reference, qsysinc]
summary: IBM i 7.5 API 功能分类与常用 API 一览（基于 apipdf.pdf 提取）
related:
  - ibm-i-api-concepts
  - ibm-i-user-space
  - ibm-i-list-api
sources:
  - title: API overview and concepts (IBM i 7.5)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/apipdf.pdf
    filed: raw/papers/apipdf.txt
created: 2026-04-19
---

# IBM i API 参考

本文档为 IBM i 7.5 **API Overview and Concepts** 的结构化摘要，包含 API 分类与常用 API 速查。

## 分类速查

| 分类 | 说明 | 典型 API |
|------|------|----------|
| **User Space** | 创建/操作用户空间对象 | `QUSCRTUS`, `QUSRTVUS`, `QUSCHGUS`, `QUSDLTUS` |
| **List API** | 返回系统对象列表 | `QUSLOBJ`, `QUSLSPL`, `QUSLJOB`, `QUSRUSAT` |
| **Object** | 对象管理 | `QUSROBJD`, `QSYLOBJP`, `QSYCRTOB` |
| **Job** | 作业管理 | `QWDRJOBD`, `QUSLJOB`, `QCLSCAN` |
| **Message** | 消息处理 | `QMHRCVPM`, `QMHSNDM`, `QCPFMSG` |
| **Profile** | 用户profile | `QSYGETP`, `QSYPUTP`, `QSYRLSPC` |
| **Exit** | 退出程序注册 | `QWVDEXR`, `QWVDAXIT`, `QUSREXI` |
| **ILE CEE** | 系统级运行时 | `CEEUTCC`, `CEEDYW`, `CEERCC0` |
| **Data Queue** | 数据队列 | `QSNDDTAQ`, `QRCVDTAQ` |
| **Product** | 软件产品管理 | `QSZCRTPD`, `QSZLICPGM` |
| **Terminal** | 终端控制 | `QTACTLDV` |
| **Directory** | 目录/IFS 操作 | `QHOpenList`, `QHRedDirectory` |

## User Space API

User Space（`*USRSPC`）是 List API 和数据存储的核心载体。

| API | 功能 |
|-----|------|
| `QUSCRTUS` | Create User Space（创建用户空间） |
| `QUSRTVUS` | Retrieve from User Space（读取用户空间） |
| `QUSCHGUS` | Change User Space（更新用户空间） |
| `QUSDLTUS` | Delete User Space（删除用户空间） |
| `QUSPTRUS` | Retrieve Pointer to User Space（获取指针） |

### QUSCRTUS 参数

```rpg
 D QUSCRTUS...
 D                  PR                  ExtPgm('QUSCRTUS')
 D  Name                          20    Const
 D  Attr                          10    Const
 D  Size                          10I 0 Const
 D  Auth                          10    Const
 D  Text                          50    Const
 D  Replace                       10    Const
 D  Error                              Like(QUSEC)
```

### User Space 通用格式头（QUSGEN）

```rpg
 D QUSG0400      DS
 D  QUSOBA                      1   4B 0  Offset to Base
 D  QUSGCSID                    5   8B 0  CCSID
 D  QUSGRTN                     9  12B 0  Return Code
 D  QUSBAVL                    13  16B 0  Bytes Available
 D  QUSBPRV                    17  20B 0  Bytes Returned
```

## List API

List API 统一模式：创建 User Space → 调用 List API → 解析头部 → 遍历条目。

### 通用列表头格式

```rpg
 D QUSL0100      DS
 D  QUSNBRLE                   1   4B 0  Number List Entries
 D  QUSSIZELE                  5   8B 0  Size Each Entry
 D  QUSOBA                     9  12B 0  Offset List Data
 D  QUSSLI                     13  13    Subset List Indicator
```

### 常用 List API

| API | 列出对象 | 格式 |
|-----|---------|------|
| `QUSLOBJ` | 库/对象列表 | `OBJL0100`, `OBJL0200` |
| `QUSLSPL` | Spooled Files 列表 | `SPLF0100`, `SPLF0200` |
| `QUSLJOB` | 作业列表 | `JOBL0100` |
| `QUSRUSAT` | 用户授权列表 | `SATC0100`, `SATC0200` |
| `QCLSCAN` | 数据库记录扫描 | — |

## Object API

| API | 功能 |
|-----|------|
| `QUSROBJD` | Retrieve Object Description（获取对象描述） |
| `QSYLOBJP` | List Objects（列出对象） |
| `QDBRTVFD` | Retrieve File Description（获取文件描述） |

### QUSROBJD 格式（ROBD0100 部分字段）

```rpg
 D QUSROBJD...
 D                 DS                  Based(pROBD)
 D  QUSOBNM                      1  10    Object Name
 D  QUSOBLB                     11  20    Library Name
 D  QUSOBTP                     21  30    Object Type
 D  QUSOBAT                     31  40    Attribute
 D  QUSOBCT                     41  46B 0  Create Timestamp
 D  QUSOBCH                     47  52B 0  Change Timestamp
```

## Job API

| API | 功能 |
|-----|------|
| `QWDRJOBD` | Retrieve Job Description（获取作业描述） |
| `QUSLJOB` | List Jobs（列出作业） |
| `QCLSCAN` | Scan Database Records（扫描数据库记录） |

### QWDRJOBD 示例（RPG 调用）

```rpg
  QSYSINC  成员:  QWDRJOBD

 D JD0100        DS                  Based(pJD)
 D  QWDRJNAM                   1  10    Job Name
 D  QWDRJNUM                   1  16    Job Number
 D  QWDRJUSN                  17  26    User Name
 D  QWDRJNBR                   1  16    Qualified Job Name
```

## Message API

| API | 功能 |
|-----|------|
| `QMHRCVPM` | Receive Program Message（接收程序消息） |
| `QMHSNDM` | Send Message（发送消息） |
| `QCPFMSG` | CPF Message（CPF 异常消息） |

### QMHRCVPM 关键参数

```rpg
 D RCV0100       DS
 D  MsgKey                        4    Message Key
 D  MsgTyp                        1  0  Message Type
 D  SndNam                       10    Sender Name
 D  SndLib                       10    Sender Library
```

## Exit Program API

Exit Program 机制用于在系统操作时调用用户自定义程序。

| API | 功能 |
|-----|------|
| `QWVDEXR` | Register Exit Point（注册退出点） |
| `QWVDAXIT` | Add Exit Program（添加退出程序） |
| `QWVDRXIT` | Remove Exit Program（删除退出程序） |
| `QUSREXI` | Retrieve Exit Information（检索退出信息） |

### Exit Point 注册示例（ILE C）

```c
#include <qwdvex_h>

int main() {
    Qwd_Exit_Entry_t  entry;
    Qus_EC_t          errcode = {sizeof(Qus_EC_t)};

    strncpy(entry.exit_pt_name, "MYEXITPT  ", 10);
    strncpy(entry.exit_pgm_name, "MYLIB/MYEXIT", 20);
    entry.pgm_fmt = "X'190'";

    QWVDAXIT(&entry, &errcode);
    return 0;
}
```

## CEE（Common Execution Environment）API

ILE 语言共享的系统级函数，在 `CEE` 服务程序中。

| 函数 | 功能 |
|------|------|
| `CEEUTCC` | 获取 UTC 日期时间 |
| `CEEDYW` | 获取工作日信息 |
| `CEERCC0` | 获取消息文本 |
| `CEEISINF` | 查询系统信息 |

### CEE 日期示例

```rpg
 DCEEUTCC...
 D                 PR                  ExtProc('CEEUTCC')
 D  DateTime                    20    Options(*Omit)
 D  Fmt                          8    Options(*Omit)
 D  Return                          Like(CEEFechaZone)

 D CEEFechaZone...
 D                 DS                  Based(pCEE)
 D  Year                        1   4B 0
 D  Month                       5   6B 0
 D  Day                         7   8B 0
```

## QSYSINC  Include 文件

`QSYSINC` 库提供所有 API 的 C/RPG include 文件：

| 成员 | 内容 |
|------|------|
| `QSYSINC/QWDRJOBD` | Job Description 结构 |
| `QSYSINC/QUSEC` | Error Code 参数结构 |
| `QSYSINC/QSYLOBJP` | Object List API 结构 |
| `QSYSINC/QMHHRS` | Message API 结构 |
| `QSYSINC/LE/CEE` | CEE 函数声明 |

### RPG 中使用 QSYSINC

```rpg
 H BNDDIR('QSYSINC')
 /include QSYSINC,QUSEC
```

## API 调用规范

### 兼容性要点

```rpg
 * 正确：使用偏移量和大小访问列表数据（向前兼容）
 c                   eval      pData = pUSpace + QUSOBA
 c                   eval      entrySize = QUSSIZELE

 * 避免：硬编码条目位置（未来可能变化）
```

### Error Code 检查模式

```rpg
 c                   if        QUSBAVL > 0
 c                   eval      API_NAME = 'QUSCRTUS'
 c                   exsr      ERRCOD
 c                   endif
```

## 文档版本说明

此 PDF 文档全称为 **API overview and concepts**，属于 IBM i 7.5 文档集的 overview 卷（**5770-SS1**）。

实际内部版本为 **v7.2**，扉页注明适用于 IBM i 7.2 及后续版本。

> 注意：PDF 版仅包含概念和示例。逐条 API 描述在 IBM i Information Center 网站上以 HTML 形式提供，不再以 PDF 形式发布。

## 相关页面

- [[ibm-i-api-concepts]] — API 核心概念详解
- [[ibm-i-user-space]] — User Space 机制
- [[ibm-i-list-api]] — List API 处理模式
