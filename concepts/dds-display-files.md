---
title: DDS for Display Files
type: concept
domain: ibm-i
tags: [dds, display-file, ui, screen-design, as400]
summary: IBM i DDS 用于显示器文件的完整语法规范，包括位置条目(1-44)和关键字条目(45-80)
related:
  - dds-physical-files
  - dds-logical-files
  - dds-keywords
sources:
  - title: DDS for Display Files (IBM i 7.2)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakcpdf.pdf
    filed:: raw/papers/dds-display-files.txt
created: 2026-04-19
---

# DDS for Display Files

显示器文件（Display File）是 IBM i 上构建 5250 绿屏界面的核心工具。DDS（Data Description Specifications）描述显示器的物理和逻辑结构，定义记录格式、字段、属性、帮助和子文件等所有界面元素。

## 文件结构概览

一份显示器文件 DDS 的行类型分为三种：

| 行号前缀 | 含义 |
|---------|------|
| `A` + 数字 | 有效 DDS（_positions 1-80_） |
| `A` + 空白 | 同上一行（续行） |
| `*` 在位置 7 | 注释行 |

```dds
     A* DISPLAY FILE EXAMPLE
     A              R MENU                    RECORD FORMAT
     A          H                            H = 隐藏记录
     A              FLDA      20I 2O  2  2   字段定义
     A N01                                 条件: 01 必须关闭
     A O 02        FLDB      22N 2B  3  2   条件: 02 或打开
     A            'CONSTANT'                常数字段
     A              FLDC      R            参考字段 (REFFLD)
```

---

## 位置条目（Positions 1–44）

### Position 7: 条件指示符（Condition Indicators）

**AND 条件**：三个指示符可直接写在同一行；超过三个时，在后续行 Position 7 写 `A`（或不写，A 为默认值）继续：

```dds
     A  01 02 03           FLDA ...
     A N01                 FLDB ...   N = NOT (01 必须关闭)
     A  04 05 06
     A  07 08 09           FLDC ...   三行 AND 连接
```

**OR 条件**：每个条件（除第一个外）必须在新行 Position 7 写 `O`：

```dds
     A  01 02 03           FLDA ...   条件1: 01 AND 02 AND 03
     A O 04 05             FLDA ...   条件2: 04 AND 05
     A O 06                FLDA ...   条件3: 06
     A                     → 01+02+03 或 04+05 或 06 满足时 FLDA 显示
```

### Position 17: 条目类型

| 值 | 含义 |
|----|------|
| _(空白)_ | 字段定义 |
| `R` | 记录格式名称行（Record format name） |
| `K` | 键字段定义（用于子文件） |
| `H` | 隐藏记录格式（Hidden record） |
| `C` | 命令关键字（Commands） |

### Position 19–28: 名称（字段名/记录名）

- 记录格式名：最长 10 字符，不能以下划线开头
- 字段名：最长 10 字符，RPG 程序中直接引用
- 子文件记录名：需配合 SFL 关键字使用

### Position 29: 参考（Reference）

| 值 | 含义 |
|----|------|
| _(空白)_ | 独立字段定义 |
| `R` | 字段引用（使用 REFFLD 引用其他记录格式的字段） |

### Position 30–34: 长度（Length）

字段字符长度，1–9999。对于编辑字/常数字段，位置 30-33 为总长，位置 34 为小数位。

### Position 35: 数据类型 + 键盘换档（Data Type + Keyboard Shift）

```
数据字面  含义           键盘换档字符
A         Character      空白=字母换档
S         Character      S=仅系统
P         Packed decimal Y=数字0-9
I         Integer        Z=零填充数字
U         unsigned       N=不可编辑(只读)
L         Date           O=系统字符
T         Time           B=左零填充
O         Graphic (DBCS) K=katakana
G         DBCS-Open      H=隐藏
Y         Numeric        M=自动移到下一个字段
X         Hexadecimal    1-4=功能键掩码
```

### Position 36–37: 小数位（Decimal Positions）

仅对数字类型（P/I/Y）有意义，`00`–`63`，表示小数位数。

### Position 38: 用途（Usage）

| 值 | 含义 |
|----|------|
| _(空白)_ | 输入/输出（I/O） |
| `O` | 仅输出（Output only） |
| `I` | 仅输入（Input only） |
| `B` | 两者皆可（Both） |
| `H` | 隐藏（Hidden，程序不显示） |

### Positions 39–44: 位置（Location）

```
位置 39-41: 行号（Line，1-27 或 1-24）
位置 42-44: 列号（Position，1-132）
```

```dds
     A              FIELDA      10   0   1  2    → 行1 列2
     A              FIELDB      10   0  27 80    → 行27 列80
     A  *NORMAL                       15  1     → 副显示尺寸下行15 列1
```

### 属性字符（Attribute Characters）

**起始属性字符**（位置 40，H 字段）控制字段显示属性：颜色、亮度、反显、下划线等。详见 [[dds-display-file-keywords]] 的 DSPATR 部分。

---

## 子文件（Subfile）

子文件是 IBM i 显示器文件最强大的特性之一——在内存中维护多条记录的列表，由程序控制滚动和选择。

### 子文件记录格式

```dds
     A          R SFLFMT                 子文件记录格式名
     A              SFL
     A              SFLPAG(10)             每页10条
     A              SFLSIZ(100)            最多100条
     A              SFLDSP                 显示子文件
     A              SFLDSPCTL              显示控制记录
     A              SFLEND(*MORE)          滚动到底部提示
     A            SFLRCDNBR               子文件记录号字段(隐藏)
     A            FIELD1      10A  O 5  2  字段1
     A            FIELD2       5Y 0O 5 13  字段2
```

### 子文件控制记录格式

```dds
     A          R CTLFMT                  控制记录格式
     A              SFLCTL(SFLFMT)         关联子文件
     A            ROLLUP                    Page Down
     A            ROLLSDWN                  Page Up
     A              SFLCSRRRN(&CSR)       返回光标所在记录号
```

### 关键子文件关键字

| 关键字 | 功能 |
|--------|------|
| [[SFL]] | 声明子文件记录格式 |
| [[SFLCTL]] | 控制记录，定义子文件属性 |
| [[SFLPAG]] | 每页显示条数 |
| [[SFLSIZ]] | 子文件总条数（配合 SFLPAG 实现多页） |
| [[SFLDSP]] | 显示子文件内容 |
| [[SFLDSPCTL]] | 显示子文件控制记录（表头等） |
| [[SFLINZ]] | 初始化子文件 |
| [[SFLCLR]] | 清空子文件 |
| [[SFLNXTCHG]] | 标记用户修改过的行 |
| [[SFLDROP]] | 折叠/展开模式切换 |
| [[SFLFOLD]] | 长行换行模式 |
| [[SFLENTER]] | Enter 键触发程序 |
| [[SFLMSGKEY]] | 子文件消息键（程序发送状态消息） |

---

## 帮助系统（Help）

显示器文件内置完整的多级帮助系统：

```dds
     A              R MENU
     A          H                            隐藏记录
     A              HLPARA(1 1 12 80)         帮助区域定义
     A              HLPRCD(RECORD1 FILEA)     帮助关联: 记录/字段
     A              HELP                      启用帮助
     A              ALTHELP(CA01)             F1 或 CA01 都触发帮助
     A              HLPTITLE('帮助标题')
     A              HLPPNLGRP(MYHELP)         链接到 HLP panel group
```

帮助层级：屏幕级 → 记录级 → 字段级 → 字段值级。

---

## 菜单栏（Menu Bar）

```dds
     A              MNUBAR(MYBAR)
     A              MNUBARDSP(MYBAR)
     A          R MENUREC
     A              MNUBARCHC(MYBAR '1' '操作')   选项1
     A              MNUBARCHC(MYBAR '2' '帮助')   选项2
     A              MNUCNL(CA01)                   取消键
```

---

## 窗口（Window）

```dds
     A              WINDOW(5 10 15 50)      行5 列10 高15 宽50
     A              WDWTITLE('窗口标题')
     A              WDWBORDER                 带边框
     A              RMVWDW                    关闭时自动清除窗口
```

---

## 与物理/逻辑文件的区别

| 特性 | 显示器文件 | 物理文件 | 逻辑文件 |
|------|-----------|---------|---------|
| 用途 | 屏幕界面 | 数据存储 | 数据视图 |
| 记录级关键字 | DSPATR, COLOR, SFL... | UNIQUE, FIFO... | JFILE, JFLD... |
| 字段属性 | 颜色/亮度/DSPATR | 数据类型/键 | 计算/连接 |
| 程序接口 | WRITE/READ/EXFMT | WRITE/READ/UPDATE | 同物理文件 |
| 是否持久 | 否 | 是 | 是 |

---

## 编译

```bash
CRTDSPF FILE(MYLIB/MYDSPF) SRCFILE(MYLIB/DDSFILE) 
# 或
CRTDSPF FILE(MYLIB/MYDSPF) SRCFILE(MYLIB/DDSFILE) SFLSIZE(1000) SFLPAG(10)
```

> **注意**：此手册版本为 IBM i 7.2（IBM i 7.5 系统亦兼容）。
