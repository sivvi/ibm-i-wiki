---
title: DDS for Printer Files
type: concept
domain: ibm-i
tags: [dds, printer-file, printing, afp, report-design]
summary: IBM i DDS 打印机文件规范，包括位置条目(1-44)、LINE/SPACE/SKIP定位、字体/颜色/条码打印
related:
  - dds-physical-files
  - dds-logical-files
  - dds-display-files
  - dds-printer-file-keywords
sources:
  - title: DDS for Printer Files (IBM i 7.2)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakdpdf.pdf
    filed: raw/papers/dds-printer-files.txt
created: 2026-04-19
---

# DDS for Printer Files

打印机文件（Printer File）用于描述 IBM i 上报表/打印输出的格式。DDS 定义打印机的记录格式、字段布局、页面控制、字体和条码等。

## 文件结构

```
     A* PRINTER FILE EXAMPLE
     A          R TITLER                   记录格式 TITLER
     A            FLD1      40        47  字段: 行1 列47
     A  30        FLD2      40        47  条件: 指示符30关闭时打印
     A          R AUTHORR
     A                                    50'by'              常数字段
     A            FIELD1    40        47  字段
     A                                    50DFT('Task Force') 默认值
     A          R PUBR                      SKIPB(58)          打印前跳到58行
     A                                    47DATE  EDTCDE(Y)   日期+编辑码
```

**规格限制：**
- 每个文件最多 1024 个记录格式
- 每个记录格式最多 32,767 个字段
- 记录格式总长度（含指示器）最多 32,767 字节

---

## 位置条目（Positions 1–44）

### Position 17: 类型

| 值 | 含义 |
|----|------|
| `R` | 记录格式名称行 |
| _(空白)_ | 字段定义行 |

### Position 35: 数据类型

打印机文件数据类型比显示器文件少，没有键盘换档概念：

| 值 | 类型 | 说明 |
|----|------|------|
| `A` | Character | 字符 |
| `P` | Packed decimal | 压缩十进制 |
| `S` | Zoned decimal | 带区十进制 |
| `I` | Integer | 整数 |
| `O` | Graphic (DBCS) | 图形字符 |
| `G` | DBCS-Open | DBCS-Open 类型 |
| `L` | Date | 日期 |
| `T` | Time | 时间 |

### Position 38: 用途（Usage）

| 值 | 含义 |
|----|------|
| _(空白)_ | 输出（Output only） |
| `O` | 输出（Output only） |

> 打印机文件只能输出，没有 `I`（输入）或 `B`（双向）选项。

### Positions 39–44: 行列位置

```
行号（1-255）或特殊值：*OVERLAY, *MAX, *LPI
列号（1-378）
```

```dds
     A            TITLE     40        1 10    行1 列10
     A            EMPNO     5  0      *LPI     随当前行距打印
     A            DETAIL    30        *OVERLAY 覆盖模式
```

---

## 页面布局控制

### LINE — 画线

```dds
     A              LINE(*RECTANGLE 5 10 20 60)   矩形: 行5 列10 到 行20 列60
     A              LINE(*BOX 5 10 20 60)         边框矩形
     A              LINE(*VERTICAL 5 10 20)       垂直线: 行5-20 列10
     A              LINE(*HORIZONTAL 5 10 60)     水平线: 行5 列10-60
```

### SPACE / SPACEA / SPACEB — 行距

```dds
SPACEA(2)    本行之后空2行
SPACEA(*LPI) 与LPI结合动态间距
SPACEB(1)    本行之前空1行（跳页控制）
SPACEA(0)    连续打印（无间距）
```

### SKIPA / SKIPB — 跳页控制

```dds
SKIPB(3)     打印前先跳到第3行（跳到下一页顶部）
SKIPA(60)    本行打印后跳到第60行（控制换页位置）
SKIPB(58)    本记录打印前跳到第58行
SKIPA(66)    本记录最后一行后跳到第66行（标准66行报表）
```

典型报表布局（66行标准）：
```dds
     A          R HEADER              SKIPB(3)
     A            REPORTNM  30        1
     A            RUNDATE  L        40
     A          R DETAIL               行距控制
     A            ACCTNO   10        1
     A            AMOUNT   12 2     15SPACEA(1)
     A          R FOOTER               SKIPB(58)
     A                                    66DATE  PAGNBR
```

### ENDPAGE — 强制换页

```dds
     A              ENDPAGE             强制换页
```

### FORCE — 强制记录输出

```dds
     A              FORCE               立即输出当前记录
```

---

## 字体与间距控制

### CPI / LPI — Characters Per Inch / Lines Per Inch

```dds
CPI(10)    10 字符/英寸（80列/行）
CPI(15)    15 字符/英寸（132列/行）
CPI(17.16) 压缩（198列/行）
LPI(6)     6 行/英寸（标准）
LPI(8)     8 行/英寸（紧密）
```

### FONT / FNTCHRSET — 字体

```dds
FONT(011)            字体011（Courier）
FONT(505)            字体505（OCR）
FNTCHRSET(37)        字形集
CDEFNT('FINANCE')    代码字体名称
FONTNAME('Courier New')  TrueType/OpenType字体
```

### CHRSIZ — 字符大小

```dds
CHRSIZ(12 12)         宽12 高12 (1/10" 单位)
CHRSIZ(14 *AUTO)      高14 自动宽度
```

### PAGRTT — 页面旋转

```dds
PAGRTT(*AUTO)    自动旋转（纵向/横向）
PAGRTT(*LANDSCAPE)
PAGRTT(*PORTRAIT)
```

---

## 条码打印（BARCODE）

> IBM i 7.2 新增：QR Code、Code 93、GS1 Databar、USPS Intelligent Mail

```dds
     A            BCFIELD    20A        1 10BARCODE(*CODE39 3 *HRI)
     A            BCFIELD    20A        1 10BARCODE(*QR 1 *QRCDF)
     A            BCFIELD    20A        1 10BARCODE(*PDF417 2 0.01)
```

**支持的条码类型：**

| 类型值 | 条码名称 |
|--------|---------|
| `*CODE39` | Code 3 of 9 |
| `*CODE128` | Code 128 |
| `*EAN13` | EAN-13 |
| `*EAN8` | EAN-8 |
| `*UPCE` | UPC-E |
| `*PDF417` | PDF417（二维） |
| `*QR` | QR Code（二维） |
| `*DATAMTX` | Data Matrix（二维） |
| `*MAXICODE` | Maxicode（二维） |
| `*GS1-128` | GS1-128 (UCC/EAN-128) |
| `*ITF14` | ITF-14 |
| `*USPOST` | USPS Intelligent Mail |
| `*ROYMAIL` | Royal Mail |

---

## 颜色与属性

### COLOR — 颜色

```dds
COLOR(*BLACK)   黑（默认）
COLOR(*BLUE)    蓝
COLOR(*RED)     红
COLOR(*GREEN)   绿
COLOR(*YELLOW)  黄
COLOR(*WHITE)   白
COLOR(*PINK)    粉
COLOR(*TURQ)    青
```

### HIGHLIGHT — 高亮

```dds
HIGHLIGHT(*NO)    普通（默认）
HIGHLIGHT(*YES)   高亮（粗体）
```

### UNDERLINE — 下划线

```dds
     A            NAME      30        1 10UNDERLINE
```

---

## AFP / Overlay / Page Segment

### OVERLAY — 叠加

```dds
     A              OVERLAY(MYFORM)      叠加预定义的AFP表单
     A              OVERLAY(MYLOGO *LAST) 在最后叠加
```

### PAGSEG — Page Segment

```dds
     A              PAGSEG(MYSEG 5 40)   页面段: 行5 列40
```

### AFPRSC — AFP Resource

```dds
     A              AFPRSC('/qFonts/myfont')  AFP资源
```

---

## 页码、日期、纸张控制

### PAGNBR — 页码

```dds
     A              PAGNBR               自动页码（3位）
     A            PGNBR     4  0      1 70PAGNBR  自定义字段页码
```

### DATE / TIME

```dds
     A                                    40DATE              当前日期
     A                                    40DATE EDTCDE(Y)   格式化日期
     A                                    40TIME              当前时间
     A            RUNDT    L            40DATFMT(*YMD)       YMD格式
```

### DRAWER — 纸张抽屉

```dds
DRAWER(1)     抽屉1
DRAWER(*AUTO) 自动选择
```

### OUTBIN — 输出纸槽

```dds
OUTBIN(*MIRROR)   镜像打印
OUTBIN(1)         纸槽1
```

### DUPLEX — 双面打印

```dds
DUPLEX(*NO)        单面
DUPLEX(*YES)       双面长边翻转
DUPLEX(*TUMBLE)    双面短边翻转
```

### PRTQLTY — 打印质量

```dDS
PRTQLTY(*DRAFT)    草稿质量
PRTQLTY(*NLQ)      Near Letter Quality
PRTQLTY(*LETTER)   书信质量
PRTQLTY(*IMAGE)    图像质量
```

### STAPLE — 装订

```dds
STAPLE(*COUNTER)   顶部左边装订
STAPLE(*STAPLE)    自动装订
```

---

## 与显示器文件的区别

| 特性 | 打印机文件 | 显示器文件 |
|------|----------|-----------|
| 方向 | 输出单向 | 输入/输出双向 |
| Position 35 | 无键盘换档 | 数据类型+键盘换档 |
| Position 38 | 只有 O（输出） | I/O/B/H |
| 页面控制 | SKIP/LINE/SPACE | 显示屏尺寸 |
| 子文件 | 无 | SFL 系统 |
| 帮助系统 | 无 | 完整 HELP 体系 |
| 菜单栏 | 无 | MNUBAR 系统 |
| 窗口 | 无 | WINDOW 系统 |
| 主要关键字 | BARCODE/OVERLAY/COLOR | DSPATR/SFL/HELP |

---

## 编译

```bash
CRTPRTF FILE(MYLIB/MYPRTF) SRCFILE(MYLIB/DDSFILE) +
   PAGESIZE(66 80) LPI(6) CPI(10)
```

> **注意**：此手册版本为 IBM i 7.2（与 7.5 兼容）。
