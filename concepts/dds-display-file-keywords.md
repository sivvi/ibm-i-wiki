---
title: DDS Display File Keywords Reference
type: concept
domain: ibm-i
tags: [dds, display-file, keywords, reference, subfile, sfl]
summary: IBM i DDS 显示器文件全部164个关键字速查，按文件级/记录级/字段级/子文件分类
related:
  - dds-display-files
  - dds-physical-files
  - dds-keywords
sources:
  - title: DDS for Display Files (IBM i 7.2)
    url: https://www.ibm.com/docs/en/ssw_ibm_i_75/pdf/rzakcpdf.pdf
    filed: raw/papers/dds-display-files.txt
created: 2026-04-19
---

# DDS Display File Keywords Reference

> 共 **164 个独立关键字**，来源：IBM i 7.2 DDS for Display Files (RZAKCPDF)
> 手册正文从 line 1866 开始（前 1800 行为目录和前言）

## 关键字级别速查

| 级别 | 含义 | 典型关键字 |
|------|------|-----------|
| **F** | 文件级（file-level） | DSPSIZ, HELP, OVERLAY, WINDOW, MNUBAR, INDARA |
| **R** | 记录级（record-level） | ALARM, ERASE, DSPATR, COLOR, SFLCTL, CLRL |
| **H** | 记录格式级（hidden record） | HLPARA, HLPRCD |
| **FLD** | 字段级（field-level） | REFFLD, ALIAS, CHECK, RANGE, VALUES, DFTVAL |

---

## 显示属性与格式

### DSPATR — Display Attribute（字段/记录级）

| 属性代码 | 含义 | 示例 |
|---------|------|------|
| `HI` | High intensity（高亮） | `DSPATR(HI)` |
| `RI` | Reverse image（反显） | `DSPATR(RI)` |
| `UL` | Underline（下划线） | `DSPATR(UL)` |
| `BL` | Blank（空格/清空） | `DSPATR(BL)` |
| `ND` | Non-display（不显示，只读） | `DSPATR(ND)` |
| `PC` | Protected（保护，不可输入） | `DSPATR(PC)` |
| `CSP` | Column separator（列分隔线） | `DSPATR(CSP)` |
| `DSPMOD` | Display only（只显示） | `DSPATR(DSPMOD)` |
| `PRoT` | Protected（保护） | `DSPATR(PR)` |

```dds
     A            FLDA      10A  I  1  2DSPATR(HI)     高亮显示
     A            FLDB      10A  I  2  2DSPATR(ND)     不显示但存在
     A            FLDC      10A  I  3  2DSPATR(RI PC)  反显+保护
```

### COLOR — Color（字段/记录级）

```dds
COLOR(WHT)  White（白）      COLOR(WHT)
COLOR(GRN)  Green（绿）      COLOR(GRN)
COLOR(TRQ)  Turquoise（青） COLOR(TRQ)
COLOR(YLW)  Yellow（黄）    COLOR(YLW)
COLOR(RED)  Red（红）       COLOR(RED)
COLOR(NDS)  Non-display（非显示）
COLOR(BLU)  Blue（蓝）      COLOR(BLU)
```

### DATE / TIME / DATFMT / DATSEP / TIMFMT / TIMSEP

```dds
     A            HIREL    L  1  1DATFMT(*DMY)     日期字段 *日日月月年年
     A            WORKTM   T  1  1TIMFMT(*HMS)      时间字段 *时时分分秒秒
     A            HIREL   L  1  1                    使用系统默认值
```

### EDTCDE / EDTMSK / EDTWRD — Edit Codes / Edit Mask / Edit Word

```dds
EDTCDE(1)   →  1,234   (逗号千分位)
EDTCDE(A)   →  $1,234  (美元符号)
EDTCDE(B)   →  1234    (空格分隔)
EDTCDE(C)   →  -1234   (负号在后)
EDTCDE(J)   →  1234    (左填零省略)

EDTWRD('  ,  .  -')     显式编辑字
EDTMSK('  ,  .  ')      编辑掩码
```

---

## 输入验证

### CHECK — Check（字段级）

```dds
CHECK(RB)   Required Any（必须输入任意字符）
CHECK(RZ)   Required Zero（必须输入数字）
CHECK(RR)   Required Alphanumeric（必须输入字母数字）
CHECK(ME)   Must Enter（必须按 Enter 或 PF 继续）
CHECK(LC)   Left Zero（数字左零压缩）
CHECK(LE)   Left Zero Eliminate（消除左零）
```

### RANGE — Range（字段级）

```dds
RANGE(1 100)         允许 1-100
RANGE(*ZERO *MAX)   允许零到最大值
```

### VALUES — Valid Values（字段级）

```dds
VALUES('Y' 'N' 'M')    仅允许 Y N M
```

### MAPVAL — Map Values（字段级）

```dds
MAPVAL(('A':'B') ('C':'D'))   输入 A→输出 B, 输入 C→输出 D
```

---

## 子文件（SFL — Subfile）关键字

共 **29 个子文件关键字**。子文件由一对记录格式组成：子文件记录（SFL）和控制记录（SFLCTL）。

### 基础子文件

```dds
     A          R SFLREC                     子文件记录格式
     A              SFL                        ← 必须（标记为子文件）
     A              SFLPAG(10)                 每页条数
     A              SFLSIZ(100)                总条数
     A              SFLDSP                     显示子文件内容
     A              SFLDSPCTL                  显示控制记录
     A              SFLEND(*MORE)              到底提示
     A            RCDNBR        4S 0H          隐藏: 子文件记录号字段
     A            FIELD1      10A  O 5  2      子文件字段
     A            FIELD2       5Y 0O 5 13

     A          R CTLFMT                     控制记录格式
     A              SFLCTL(SFLREC)             ← 必须（关联子文件）
     A              SFLPAG(10)                 ← 与子文件 SFLPAG 一致
     A              SFLSIZ(100)
     A            PGMKEY       5A              编程键字段
     A            ROLLUP                          PageDown 滚动
     A            ROLLSDWN                       PageUp 滚动
```

### 子文件滚动与导航

| 关键字 | 功能 | 补充 |
|--------|------|------|
| `SFLFOLD` | 长行换行模式（默认截断） | `SFLFOLD(CA01)` 切换折叠 |
| `SFLDROP` | 折叠/展开切换 | `SFLDROP(CA01)` |
| `SFLSCROLL` | 相对滚动 | `SFLSCROLL(&RRN)` |
| `SFLROLVAL` | 滚动值（滚动行数） | 默认 1 行 |
| `SFLENTER` | Enter 键触发程序 | `SFLENTER(CA01)` 可指定替代键 |
| `SFLRNA` | 无记录时显示提示 | 子文件为空时显示 |
| `SFLDLT` | 支持删除操作 | 与 `SFLNXTCHG` 配合 |
| `SFLCLR` | 程序主动清空子文件 | |

### 子文件模式与选择

| 关键字 | 功能 |
|--------|------|
| `SFLMODE(*NORESP)` | 禁止输入（只读） |
| `SFLRTNSEL` | 返回用户选择的选项 |
| `SFLSNGCHC` | 单选列表模式 |
| `SFLMLTCHC` | 多选列表模式 |
| `SFLCHCCTL` | 选择字段属性 |
| `SFLCSRRRN` | 返回光标所在记录号 |

### 子文件消息

| 关键字 | 功能 |
|--------|------|
| `SFLMSGKEY` | 消息键字段 |
| `SFLMSGRCD` | 消息记录格式 |
| `SFLPGMQ` | 程序消息队列 |

---

## 帮助关键字

| 关键字 | 级别 | 功能 |
|--------|------|------|
| `HELP` | F/R | 启用帮助 |
| `ALTHELP(CAnn)` | F | 替代帮助键 |
| `HLPARA(r c w h)` | H | 帮助区域定义 |
| `HLPRCD(rec fld)` | H | 关联帮助记录/字段 |
| `HLPDOC(docid)` | F/R | 关联帮助文档 |
| `HLPPNLGRP(pnl)` | F/R | 关联 panel group |
| `HLPTITLE(text)` | F | 帮助标题 |
| `HLPSCHIDX(idx)` | F | 帮助索引 |
| `HLPID(helpid)` | F | 帮助 ID |
| `HLPSEQ(seq)` | F | 帮助顺序 |
| `HLPFULL` | F | 全屏帮助 |
| `HLPCLR` | F | 清除帮助 |
| `HLPEXCLD` | F | 排除字段级帮助 |
| `HLPRTN` | F/R | 帮助返回键 |

---

## 菜单栏关键字

| 关键字 | 功能 |
|--------|------|
| `MNUBAR(barname)` | 声明菜单栏 |
| `MNUBARDSP(barname)` | 显示菜单栏 |
| `MNUBARCHC(barname 'key' 'text')` | 添加选项 |
| `MNUBARSEP` | 分隔符 |
| `MNUBARSW(key)` | 切换键 |
| `MNUCNL(key)` | 取消键 |

---

## 窗口关键字

```dds
WINDOW(row col height width)    定义窗口
WDWTITLE('标题')                窗口标题
WDWBORDER                       边框
RMVWDW                          关闭时清除
```

---

## 文件级关键字

| 关键字 | 功能 |
|--------|------|
| `DSPSIZ(27 132 *LARGE 24 80 *NORMAL)` | 显示器尺寸 |
| `INDARA` | 指示器独立存储区 |
| `OVERLAY` | 覆盖而非清屏 |
| `KEEP` | 退出后保持显示 |
| `HOME` | 光标归位 |
| `CLEAR` | 清屏 |
| `CLRL(*ALL)` | 清特定行 |
| `ERASE` | 擦除指定区域 |
| `PRINT` | 支持打印 |
| `OPENPRT` | 打开时默认打印 |
| `PASSRCD` | 传递记录 |
| `CSRLOC(row col)` | 光标位置返回 |
| `RTNCSRLOC` | 返回光标位置 |
| `FLDCSRPRG` | 光标推进字段 |
| `LOGINP / LOGOUT` | 记录输入/输出 |

---

## 记录级关键字

| 关键字 | 功能 |
|--------|------|
| `ALARM` | 显示时响铃 |
| `MSGID(msgid)` | 关联消息 ID |
| `ERRMSG(msgtext)` | 错误消息 |
| `ERRMSGID(msgid)` | 错误消息 ID |
| `MSGALARM` | 带消息时响铃 |
| `MSGLOC(row col)` | 消息行位置 |
| `INZRCD` | 初始化记录 |
| `INZINP` | 初始化输入 |
| `FRCDTA` | 强制输出 |
| `SETOF(01 02)` | 关闭指示器 |
| `PUTRETAIN` | 保留上次值 |
| `PROTECT` | 保护整条记录 |
| `LOCK` | 锁定记录 |
| `UNLOCK` | 解锁 |
| `SLNO` | 起始行号 |

---

## 字段级关键字

| 关键字 | 功能 |
|--------|------|
| `REFFLD(field) / REFFLD(rec fld)` | 引用字段 |
| `REF(lib/file rec)` | 引用文件 |
| `DFT(value)` | 默认值 |
| `DFTVAL(expr)` | 默认值表达式 |
| `ALIAS(name)` | 替代名称 |
| `DUP` | 允许复制 |
| `BLANKS` | 允许空白 |
| `CHANGE` | 允许修改 |
| `LOWER` | 允许小写 |
| `AUTO` | 自动提交 |
| `COMP(gt lt eq ...)` | 比较关系 |
| `CMP(oper val)` | 比较 |
| `CHGINPDFT(attr)` | 改变输入默认 |
| `DATTIM` | 日期时间自动填充 |
| `USER` | 当前用户自动填充 |
| `SYSNAME` | 系统名自动填充 |
| `VALNUM` | 验证数字 |
| `CHKMSGID` | 验证消息 ID |
| `GETRETAIN` | 保留 GET 值 |
| `OVRATR` | 覆盖属性 |
| `OVRDTA` | 覆盖数据 |
| `PUTOVR` | 显式覆盖 |
| `NOCCSID` | 无 CCSID |
| `DATE / TIME` | 格式化日期/时间 |
| `DATFMT / TIMFMT` | 日期/时间格式 |
| `BLKFOLD` | 空格折叠 |
| `ENTFLDATR` | 输入字段属性 |
| `DSPMOD` | 显示模式 |
| `DSPRL` | 从右到左 |
| `WRDWRAP` | 单词换行 |
| `USRDFN(n)` | 用户定义属性 |
| `USRDSPMGT` | 用户显示管理 |
| `USRRSTDSP` | 恢复显示 |
| `HTML(text)` | HTML 内容 |
| `RETLCKSTS` | 保留锁状态 |
| `RTNDTA` | 返回数据 |
| `MLTCHCFLD` | 多选字段 |
| `PSHBTNFLD` | 按钮字段 |
| `PSHBTNCHC` | 按钮选项 |
| `CHCACCEL` | 选择加速器 |
| `CHOICE(text)` | 选择文本 |
| `CNTFLD` | 续输入字段 |
| `SNGCHCFLD` | 单选字段 |

---

## 键盘控制关键字

| 关键字 | 功能 |
|--------|------|
| `VLDCMDKEY` | 有效命令键 |
| `MOUBTN(btn ... cf)` | 鼠标按钮映射 |
| `INVITE` | 邀请输入 |
| `CSRINPONLY` | 光标仅到输入位 |

---

## CCSID / Unicode / DBCS

| 关键字 | 功能 |
|--------|------|
| `NOCCSID` | 禁用 CCSID |
| `CHRID(*JOB)` | 字符 ID 来自作业 |
| `CCSID(n)` | 指定 CCSID |
| `ALWGPH` | 允许图形字符 |
| `IGCALTTYP` | DBCS 替代类型 |
| `IGCCNV` | DBCS 转换 |
| `GRDATR / GRDBOX / GRDLIN` | 网格属性 |

---

## 使用限制（冲突关键字）

| 关键字 | 互斥 |
|--------|------|
| `ALTPAGEDWN / ALTPAGEUP` | 不能与 `CA01` / `CF01` 共存 |
| `SFLDROP(CA01)` | 不能与 `ALTHELP(CA01)` 共存 |
| `SFLENTER(CF01)` | 不能与 `ALTHELP(CF01)` 共存 |
| `MNUCNL(CA01)` | 不能与 `ALTHELP(CA01)` 共存 |
| `DSPSIZ(*DS3)` 主尺寸 | 不能与 `*DS3` 条件名共用 |
| `DSPSIZ(*DS4)` 主尺寸 | 不能与 `*DS4` 条件名共用 |
| `DSPATR(ND)` | 不能与输入字段共用 |
| `DSPATR(PC)` | 保护字段，不能输入 |
