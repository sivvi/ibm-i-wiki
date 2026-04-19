---
title: RPG XML and JSON Processing
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, xml, json, data-interchange, parsing]
sources: [raw/papers/rpg-reference-7.5.txt, raw/papers/rpg-programmers-guide-7.5.txt]
related:
  - rpg-iv-bifs
  - rpg-file-processing
  - rpg-error-handling
---

# RPG XML/JSON 处理

RPG 提供完整的 XML 解析（XML-INTO / XML-SAX）和通用数据格式解析（DATA-INTO / DATA-GEN），支持 JSON、CSV 等层次化数据格式与 RPG 变量之间的双向转换。

## 两种解析方式对比

| 方式 | 操作码 | 适用场景 |
|------|--------|---------|
| **直接映射** | `XML-INTO` | 已知 XML 结构，映射到 RPG 变量/DS |
| **事件驱动** | `XML-SAX` | 结构未知或需要细粒度控制 |
| **通用解析** | `DATA-INTO` | JSON、CSV 等层次化格式 |
| **通用生成** | `DATA-GEN` | RPG 变量导出为 JSON、CSV |

## %XML BIF — XML 文档描述

所有 XML 操作码的第二个操作数必须是 `%XML`：

```rpgle
%XML(xmldocument {: options})
```

- `xmldocument`：XML 字符串，或 IFS 文件路径（需配合 `doc=file`）
- options：形如 `'option1=value1 option2=value2'`，大小写不敏感

## XML-INTO — 直接映射

```rpgle
XML-INTO{(E)} variable %XML(xmlDoc {: options});
XML-INTO{(E)} %HANDLER(handler : commArea) %XML(xmlDoc {: options});
```

将 XML 文档直接读取到 RPG 变量（通常是数据结构），字段名与 XML 元素名自动对应。

### 示例：解析到数据结构

XML 文件 `cpyA.xml`：
```xml
<copyinfo>
  <from><name>MASTFILE</name><lib>CUSTLIB</lib></from>
  <to><name>MYFILE</name><lib>*LIBL</lib></to>
</copyinfo>
```

RPG 代码：
```rpgle
D copyInfo       ds                  QUALIFIED
D   from                            
D     name                    10A
D     lib                     10A
D   to                              
D     name                    10A
D     lib                     10A
 /FREE
 xml-into copyInfo %XML('cpyA.xml' : 'doc=file');
 // copyInfo.from.name = 'MASTFILE  '
 // copyInfo.from.lib  = 'CUSTLIB   '
 // copyInfo.to.name   = 'MYFILE    '
 // copyInfo.to.lib    = '*LIBL     '
 /END-FREE
```

### XML-INTO 选项

| 选项 | 默认值 | 说明 |
|------|--------|------|
| `doc` | `string` | `doc=file` 表示 IFS 文件路径 |
| `allowextra` | `no` | `yes`：忽略目标变量不存在的 XML 元素 |
| `allowmissing` | `no` | `yes`：允许目标变量有但 XML 缺少的元素 |
| `case` | `lower` | `any`：XML 元素名大小写不敏感 |
| `ccsid` | `best` | 解析时 CCSID（`ccsid=1200` for UCS-2）|
| `countprefix` | — | 为数组元素数量生成额外字段（e.g., `countprefix=CNT_`）|
| `datasubf` | — | 指定接收混合内容（文本+属性）子字段名 |
| `fileccsid` | `utf8` | IFS 文件 CCSID（`fileccsid=0` for job CCSID）|
| `ns` | `keep` | XML 命名空间处理 |
| `nsprefix` | — | 处理命名空间前缀 |
| `output` | — | 控制输出行为 |
| `path` | — | 从指定 XML 路径开始解析 |
| `renameprefix` | — | 重命名元素前缀 |
| `trim` | `all` | `none`：保留空格；`all`：去除首尾空格 |

## XML-SAX — 事件驱动解析

```rpgle
XML-SAX{(E)} %HANDLER(eventHandler : commArea) %XML(xmldoc {: options});
```

SAX 解析器逐字符扫描 XML，遇到事件（如开始标签、属性、文本内容等）时调用 `eventHandler` 过程。

### 事件类型

| 事件常量 | 说明 |
|---------|------|
| `*XML_START_DOCUMENT` | 文档开始 |
| `*XML_END_DOCUMENT` | 文档结束 |
| `*XML_START_ELEMENT` | 元素开始（附属性名/值）|
| `*XML_END_ELEMENT` | 元素结束 |
| `*XML_CHARACTERS` | 文本内容 |
| `*XML_PROCESSING_INSTRUCTION` | 处理指令 |
| `*XML_DOCTYPE_DECL` | DOCTYPE 声明 |
| `*XML_COMMENT` | 注释 |
| `*XML_CDATA` | CDATA 段 |

### 事件处理过程原型

```rpgle
D xmlEvent        S              5I 0
D xmlParms        DS                 qualified
D   event                       10I 0
D   level                       10I 0
D   name                      256A
D   attributes                 256A
 /FREE
 // 检查事件类型
 SELECT;
 WHEN xmlParms.event = _QRNU_XMLSAX(*XML_START_ELEMENT);
   // 处理开始标签
 WHEN xmlParms.event = _QRNU_XMLSAX(*XML_CHARACTERS);
   // 处理文本
 ENDSL;
 /END-FREE
```

> 调试时使用 `DEBUG(*XMLSAX)` 控制关键字，数组 `_QRNU_XMLSAX` 包含所有事件名。

### XML-SAX 选项

| 选项 | 说明 |
|------|------|
| `doc` | `string` 或 `file` |
| `ccsid` | 解析时 CCSID |

### XML-SAX 示例：处理未知结构

```rpgle
D saxHandler      PI
D   xmlEvent                    10I 0
D   xmlLevel                    10I 0
D   xmlName                    256A
D   xmlData                    512A
 /FREE
 SELECT;
 WHEN xmlEvent = _QRNU_XMLSAX(*XML_START_ELEMENT);
   IF xmlName = 'customer';
     // 开始处理 customer 元素
   ENDIF;
 WHEN xmlEvent = _QRNU_XMLSAX(*XML_CHARACTERS);
   // 文本内容在 xmlData 中
 ENDSL;
 /END-FREE
```

```rpgle
XML-SAX %HANDLER(saxHandler : xmlParms)
        %XML('customer.xml' : 'doc=file');
```

## DATA-INTO — 解析 JSON/CSV

```rpgle
DATA-INTO{(EH)} target document parser
```

类似 XML-INTO，但用于**任意层次化数据格式**（JSON、CSV 等）。需要指定解析器（parser）。

```rpgle
DATA-INTO customer %DATA('customer.json' : 'doc=file')
                %PARSER('MYLIB/MYJSONPARS');
```

支持的格式由使用的解析器决定。RPG 提供内置 JSON 解析器（通过 `*JSM` 或第三方）。

## DATA-GEN — 生成 JSON/CSV

```rpgle
DATA-GEN{(EH)} source document generator
```

从 RPG 变量生成结构化文档（JSON、CSV）：

```rpgle
DATA-GEN genData %DATA('output.json' : 'doc=file')
                %GEN('MYLIB/MYJSONGEN');
```

## XML 错误码

解析失败时抛出 `RNX0351`，附加错误码：

| 错误码 | 含义 |
|-------|------|
| 1 | 无效字符（空白区外）|
| 2-7 | 标记格式错误 |
| 8 | CDATA 格式错误 |
| 9 | 注释格式错误 |
| 10 | 注释中 `--` 错误 |
| 11-13 | 处理指令/引用错误 |
| 14-36 | 声明/元素语法错误 |
| 300 | 文档不完整 |
| 301 | handler 返回非零 |
| 302 | CCSID 不支持或首字符非 `<` |
| 303 | 文档过大 |

## XML 解析器限制

- RPG 字符变量最长 16,773,104 字节；超长数据需先写 IFS 临时文件
- 单字节 CCSID 最大 2,147,483,408 字符；UCS-2 最大 1,073,741,704 字符
- 支持的 EBCDIC CCSID：1047, 37, 1140-1149 等
- 支持的 ASCII CCSID：819, 813, 920
- Unicode CCSID：1200, 13488, 17584
- 仅支持 5 个预定义实体：`&amp; &apos; &gt; &lt; &quot;`
- **不支持命名空间**，冒号被忽略
- **不支持 DTD**，DOCTYPE 内容作为事件数据传递

## IFS 临时文件方案（大XML处理）

当 XML 数据超过 RPG 变量长度时，先写入 IFS：

```rpgle
// 写入 IFS 临时文件
ifsWrite('/home/mydir/temp.xml' : xmlPtr : xmlLen : 37 : 37);
// 解析
xml-into ds %XML('/home/mydir/temp.xml' : 'doc=file');
// 删除临时文件
rmvlnk '/home/mydir/temp.xml';
```

## 相关 BIF

- `%XML` — 描述 XML 文档
- `%HANDLER` — 事件处理过程包装器
- `%DATA` — DATA-INTO/DATA-GEN 文档描述

## 相关页面

- [[rpg-iv-bifs]] — 内置函数完整参考
- [[rpg-error-handling]] — 错误处理
- [[rpg-file-processing]] — 文件处理基础
