---
title: DDS Printer File Keywords Reference
type: concept
domain: ibm-i
tags: [dds, printer-file, keywords, reference, afp, barcode]
summary: IBM i DDS 打印机文件全部65个关键字速查
related:
  - dds-printer-files
  - dds-display-file-keywords
  - dds-keywords
sources:
  - title: DDS for Printer Files (IBM i 7.2)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakdpdf.pdf
    filed: raw/papers/dds-printer-files.txt
created: 2026-04-19
---

# DDS Printer File Keywords Reference

> 共 **65 个关键字**，来源：IBM i 7.2 DDS for Printer Files (RZAKDPDF)

---

## 页面布局

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `LINE` | R | 画线/矩形/边框 |
| `SKIPA` | R | 打印后跳行 |
| `SKIPB` | R | 打印前跳行 |
| `SPACEA(n)` | R/FLD | 打印后空 n 行 |
| `SPACEB(n)` | R/FLD | 打印前空 n 行 |
| `ENDPAGE` | R | 强制换页 |
| `FORCE` | R | 立即输出当前记录 |

---

## 页面定义

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `CPI(cpi)` | F | Characters Per Inch（10/15/17.16） |
| `LPI(lpi)` | F | Lines Per Inch（6/8） |
| `PAGRTT(*AUTO)` | F/R | 页面旋转 |
| `PAGESIZE(lines cols)` | F | 页面大小（默认 66 80） |
| `ZFOLD` | F | Z折页（纵向变横向） |

---

## 字体与字符

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `FONT(fontid)` | R/FLD | 字体 ID（011/505 等） |
| `FONTNAME('name')` | F | TrueType/OpenType 字体名 |
| `FNTCHRSET(id)` | F | 字形集 ID |
| `CDEFNT(name)` | F | 代码字体名 |
| `CHRSIZ(w h)` | R/FLD | 字符尺寸（1/10" 单位） |
| `DFNCHR(name)` | F | 自定义字符定义 |
| `TXTRTT(degrees)` | R/FLD | 文字旋转（0/90/180/270） |
| `BLKFOLD` | FLD | 空格折叠 |
| `CHRID(*JOB)` | F | 字符标识 |

---

## 条码打印

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `BARCODE(type opts...)` | FLD | 条码打印（类型+高度+方向+参数） |

**条码类型：**

| type 参数 | 条码 |
|-----------|------|
| `*CODE39` | Code 3 of 9 |
| `*CODE128` | Code 128 |
| `*EAN13` | EAN-13 |
| `*EAN8` | EAN-8 |
| `*UPCE` | UPC-E |
| `*PDF417` | PDF417 二维码 |
| `*QR` | QR Code 二维码 |
| `*DATAMTX` | Data Matrix |
| `*MAXICODE` | Maxicode |
| `*GS1-128` | GS1-128 (UCC/EAN-128) |
| `*ITF14` | ITF-14 |
| `*USPOST` | USPS Intelligent Mail |
| `*ROYMAIL` | Royal Mail Red Tag |
| `*GS1DTRBR` | GS1 Databar |

**常用选项：**

```dds
BARCODE(*CODE39 3)                  高度3行，水平
BARCODE(*QR 1 *QRCDF)               QR码，1/10"高，数据压缩
BARCODE(*PDF417 2 0.01)             PDF417，2"高，0.01"宽
BARCODE(*CODE128 *HRZ *NOAST)       水平，不带星号
```

---

## 颜色与属性

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `COLOR(name)` | R/FLD | 颜色（`*BLACK/*BLUE/*RED/*GREEN/*YELLOW/*WHITE/*PINK/*TURQ`） |
| `HIGHLIGHT(*YES)` | R/FLD | 高亮/粗体 |
| `UNDERLINE` | R/FLD | 下划线 |
| `INDTXT(01 'text')` | R | 指示器文本 |

---

## AFP 资源

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `OVERLAY(name)` | R | 叠加 AFP 表单 |
| `PAGSEG(name row col)` | R/FLD | 页面段 |
| `AFPRSC(path)` | F | AFP 资源路径 |
| `GDF(name)` | R | 图形数据文件 |

---

## 页码、日期、时间

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `PAGNBR` | R/FLD | 自动页码（3位） |
| `DATE` | FLD | 当前系统日期 |
| `TIME` | FLD | 当前系统时间 |
| `DATFMT(*YMD)` | FLD | 日期格式 |
| `DATSEP('/')` | FLD | 日期分隔符 |
| `TIMFMT(*HMS)` | FLD | 时间格式 |
| `TIMSEP(':')` | FLD | 时间分隔符 |

---

## 编辑

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `EDTCDE(code)` | FLD | 编辑码（1/J/A/B/C/K 等） |
| `EDTWRD('edit-word')` | FLD | 编辑字 |
| `DLTEDT` | FLD | 删除编辑码 |
| `DFT(value)` | FLD | 默认值 |
| `DFTVAL(expr)` | FLD | 默认值表达式 |
| `FLTFIXDEC` | F | 浮点→定点转换 |
| `FLTPCN(precision)` | F | 浮点精度 |

---

## 纸张与输出

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `DRAWER(n/*AUTO)` | R | 纸张抽屉 |
| `OUTBIN(name)` | R | 输出纸槽 |
| `DUPLEX(*YES/*NO/*TUMBLE)` | F | 双面打印 |
| `PRTQLTY(*DRAFT/*NLQ/*LETTER)` | F | 打印质量 |
| `STAPLE(mode)` | R | 装订方式 |

---

## 数据转换

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `CVTDTA(*EBCDIC)` | F | 数据转换 |
| `TRNSPY` | FLD | 透明度传输 |

---

## 字段引用

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `REF(lib/file)` | FLD | 引用文件 |
| `REFFLD(field)` | FLD | 引用字段 |
| `REFPOS(*INHERIT)` | FLD | 继承引用位置 |
| `RELPOS(row col)` | FLD | 相对位置 |
| `POSITION(row col)` | FLD | 绝对行列位置 |
| `ALIAS(name)` | FLD | 替代字段名 |

---

## 记录级控制

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `INDARA` | F | 指示器独立存储区 |
| `INDTXT(indicator 'text')` | R | 条件文本 |
| `MSGCON(msgid)` | R | 消息常量 |
| `TEXT('description')` | F/R | 文件/记录描述 |
| `DOCIDXTAG(tag)` | F | 文档索引标签 |

---

## 页组（Page Groups）

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `STRPAGGRP(name)` | R | 开始页组 |
| `ENDPAGGRP` | R | 结束页组 |

---

## 数据映射

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `INVDTAMAP(name)` | F | 调用数据映射 |
| `INVMMAP(name)` | F | 调用介质映射 |

---

## CCSID / Unicode / DBCS

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `CCSID(n)` | F | 编码字符集标识 |
| `UNISCRIPT(script)` | R/FLD | Unicode 文字布局 |
| `IGCALTTYP` | FLD | DBCS 替代类型 |
| `IGCANKCNV` | FLD | 字母数字→DBCS 转换 |
| `IGCCDEFNT` | FLD | DBCS 代码字体 |
| `IGCCHRRTT` | FLD | DBCS 字符旋转 |
| `DFNLIN` | FLD | DBCS 行定义 |

---

## 键盘控制（用于 5250 打印机模拟）

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `VLDCMDKEY` | F | 有效命令键 |

---

## 关键字互斥

| 关键字 | 冲突 |
|--------|------|
| `LINE(*BOX)` 和 `LINE(*RECTANGLE)` | 不能在同一位置叠加 |
| `OVERLAY(*LAST)` | 不能与其他 OVERLAY 混用 |
| `ZFOLD` | 不能与 `PAGRTT(*AUTO)` 混用 |
| `DRAWER(*AUTO)` | 与 `OUTBIN` 互斥 |
