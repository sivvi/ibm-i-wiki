---
title: IBM i User Space and List API
type: concept
domain: ibm-i
tags: [ibm-i, user-space, list-api, api, ile]
summary: IBM i User Space 机制与 List API 的标准处理模式，包括指针/非指针两种访问方式
related:
  - ibm-i-api-concepts
  - ibm-i-api-reference
sources:
  - title: API overview and concepts (IBM i 7.5)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/apipdf.pdf
    filed: raw/papers/apipdf.txt
created: 2026-04-19
---

# IBM i User Space 与 List API

User Space 是 IBM i 中最核心的 API 数据容器。理解它的工作机制，是掌握 IBM i API 编程的基础。

## 什么是 User Space

User Space（`*USRSPC`）是一个线性字节数组的命名对象，专门用于：

- **List API** 的返回数据存储
- **程序间数据交换**
- **动态数据结构**的创建与操作

创建后，大小固定，内容可随时覆写。

## 创建 User Space

```rpg
 D/COPY QSYSINC/QUSEC
 D QUSCRTUS...
 D                  PR                  ExtPgm('QUSCRTUS')
 D  SpaceName                     20    Const
 D  Attr                          10    Const  Options(*VarSize)
 D  Size                          10I 0 Const
 D  Auth                          10    Const
 D  Text                          50    Const
 D  Replace                       10    Const
 D  ErrorCode                          Like(QUSEC)

 D usName         S             20
 D usAttr         S             10    inz('USRSPC   ')
 D usSize         S             10I 0 inz(65536)
 D usAuth         S             10    inz('*ALL      ')
 D usText         S             50    inz('API Work Space')
 D usRepl         S             10    inz('*YES')
 D ec             DS                  Like(QUSEC)

  /free
   usName = 'MYWORKLIB MYUSPACE';
   QUSCRTUS(usName: usAttr: usSize: usAuth:
             usText: usRepl: ec);
   if ec.QUSBAVL > 0;
     // error
   endif;
  /end-free
```

## User Space 通用格式头

无论哪种 List API，结果都遵循通用头结构：

| 偏移 | 字段名 | 类型 | 说明 |
|------|--------|------|------|
| 1-4 | `QUSOBA` | Binary(4) | 基准地址偏移量 |
| 5-8 | `QUSGCSID` | Binary(4) | CCSID |
| 9-12 | `QUSGRTN` | Binary(4) | 返回码 |
| 13-16 | `QUSBAVL` | Binary(4) | 可用字节数 |
| 17-20 | `QUSBPRV` | Binary(4) | 已返回字节数 |

## List API 通用数据头（QUSL0100）

List API 在 User Space 中写入两个部分：通用头 + 数据条目区。

| 偏移 | 字段名 | 类型 | 说明 |
|------|--------|------|------|
| 1-4 | `QUSNBRLE` | Binary(4) | 列表条目数 |
| 5-8 | `QUSSIZELE` | Binary(4) | 每个条目的大小 |
| 9-12 | `QUSOBA` | Binary(4) | 数据区相对 User Space 起始的偏移量 |
| 13 | `QUSSLI` | Char(1) | 子集列表指示符 |

## 两种访问模式

### 模式 1：指针模式（推荐）

```rpg
  // 获取 User Space 指针
  QUSPTRUS( SpaceName : pUSpace : ec );

  // 读取列表头
  pListHeader = pUSpace + QUSOBA;
  nbrEntries = QUSNBRLE;
  entrySize  = QUSSIZELE;

  // 定位到数据区
  pData = pUSpace + QUSOBA;

  // 遍历条目
  dow nbrEntries > 0;
    // 处理当前条目
    pData = pData + entrySize;
    nbrEntries = nbrEntries - 1;
  enddo;
```

### 模式 2：非指针模式（兼容 OPM RPG）

```rpg
  // 通过偏移量计算地址
  pData = %addr(USSpace) + offset;

  // 读取字段（需要模板 DS）
  nbrEntries = QUSNBRLE;

  // 移动到下一个条目
  offset = offset + entrySize;
```

### 指针 vs 非指针

| 维度 | 指针模式 | 非指针模式 |
|------|---------|-----------|
| 支持环境 | ILE（C/RPG/COBOL） | OPM + ILE |
| 灵活性 | 高，可动态计算 | 需预先知道布局 |
| 代码复杂度 | 较低 | 较高 |
| 推荐场景 | 新代码、复杂结构 | 遗留 OPM 代码 |

## 完整示例：ILE RPG 调用 List API

```rpg
  // 列出库中的所有文件
 D/copy QSYSINC/QUSEC
 D/copy QSYSINC/QUSCRTUS
 D/copy QSYSINC/QUSLOBJ
 D/copy QSYSINC/QUSL0100

 D USPName        S             20
 D pUSpace        S               *
 D ec             DS                  Like(QUSEC)
 D header         DS                  Based(pUSpace + QUSOBA)
 D                                     Like(QUSL0100)
 D pEntries       S               *
 D entry          DS                  Based(pEntries)
 D                                     Like(QSYLOBJ_ENT)

  /free
   USPName = 'QTEMP LISPOBJ  ';
   QUSCRTUS(USPName: '*USRSPC': 65536:
             '*ALL': 'List objects': '*YES': ec);
   if ec.QUSBAVL > 0;
     dump('Error creating US');
     return;
   endif;

   QUSLOBJ(USPName: 'OBJL0100': ec:
           '*ALL': '*ALL': '*FILE   ');

   QUSPTRUS(USPName: pUSpace: ec);

   pEntries = pUSpace + header.QUSOBA;

   for i = 1 to header.QUSNBRLE;
     dumpa(pEntries);
     pEntries = pEntries + header.QUSSIZELE;
   endfor;
  /end-free
```

## QSYLOBJ_ENT 条目结构（OBJL0100）

```rpg
 D QSYLOBJ_ENT...
 D                 DS                  Based(pEntry)
 D  QUSOBNM                      1  10    Object Name
 D  QUSOBLB                     11  20    Library Name
 D  QUSOBTP                     21  30    Object Type
 D  QUSOBAT                     31  40    Object Attribute
 D  QUSOBCT                     41  46    Create Timestamp
 D  QUSOBCH                     47  52    Change Timestamp
 D  QUSOBTX                     53  82    Text Description
```

## 处理可变长度条目

部分 List API（如 `QUSLSPL`）支持 **可变长度条目**（Variable Length Entry）：

```rpg
  // 可变长度条目格式：以长度字段开头
 D QUS-VLEN-REC-4...
 D                 DS
 D  RECLEN                       1   4B 0  Length of this record
 D  RECDATA                      5      *  Start of data

  // 读取时先取长度，再推进指针
  eval      entryLen = QUS-VLEN-LEN;
  pNext = pCurrent + entryLen;
```

## List API 返回 0 条目的处理

```rpg
  QUSPTRUS(USPName: pUSpace: ec);

  // 检查 User Space 头部
  if QUSBAVL = 0;
    // API 调用本身出错
    return;
  endif;

  // 检查列表条目数
  pListHdr = pUSpace + QUSOBA;
  if QUSNBRLE = 0;
    // 正常情况：无匹配条目
    return;
  endif;
```

## User Space 的生命周期

| 阶段 | 操作 | API |
|------|------|-----|
| 创建 | 分配固定大小字节数组 | `QUSCRTUS` |
| 填充 | List API 写入数据 | `QUSLOBJ`, `QUSLSPL`, 等 |
| 读取 | 获取指针或偏移量访问 | `QUSRTVUS`, `QUSPTRUS` |
| 更新 | 覆写内容 | `QUSCHGUS` |
| 删除 | 释放对象 | `QUSDLTUS` |

> `QUSCHGUS` 仅用于更新 User Space 的元数据（如最后修改日期），不用于写入 List API 数据。List API 直接写入已创建的 User Space。

## 最佳实践

1. **始终使用指针模式**（ILE 环境）：代码更清晰，兼容性更好
2. **始终检查 `QUSBAVL`**：确认 API 调用是否成功
3. **使用 `QUSSIZELE` 而不是硬编码**：`entrySize = QUSSIZELE` 确保向前兼容
4. **不要假设条目数 > 0**：必须处理空列表情况
5. **User Space 大小预留**：`65536` 字节是最常用的初始大小

## 相关页面

- [[ibm-i-api-concepts]] — API 核心概念
- [[ibm-i-api-reference]] — API 分类与速查
