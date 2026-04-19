---
title: "DDS Display Files Reference"
source: "IBM i 7.2 Programming - DDS for Display Files"
category: reference
version: "IBM i 7.2"
license: "IBM © Copyright 1999, 2013"
parsed_from: "/home/admin/wiki/raw/papers/dds-display-files.txt"
keywords_count: 100+
---

# DDS Display Files Reference

DDS (Data Description Specifications) for display files defines display file record formats, fields, and keywords for IBM i applications.

## Overview

Display files in DDS are defined with file-level, record-level, help-level, and field-level entries. The maximum number of record formats in a display file is **1024**, with a maximum of **32,763 fields** per record format.

## Positional Entries (Positions 1-44)

### Position Summary

| Positions | Entry Type | Description |
|-----------|------------|-------------|
| 1-5 | Sequence Number | Optional, documentation only |
| 6 | Form Type | Use `A` for DDS form |
| 7 | Comment | Use `*` to identify comment lines |
| 7-16 | Option Indicators | 01-99, conditioning for fields/keywords |
| 17 | Name Type | `R` for record format, blank for field |
| 18 | Reserved | Leave blank |
| 19-28 | Name | Record format or field name |
| 29 | Reference | `R` to reference a previously defined field |
| 30-34 | Length | Field length (bytes) |
| 35 | Data Type / Keyboard Shift | Character, numeric, date, time, etc. |
| 36-37 | Decimal Positions | For numeric fields |
| 38 | Usage | I, O, B, M, H, P |
| 39-44 | Location | Line (39-41) and Position (42-44) |

### Option Indicators (Positions 7-16)

- **AND Condition**: Multiple indicators must all be on/off
- **OR Relationship**: Any one condition satisfied selects field/keyword
- Maximum: 9 indicators per condition, 9 conditions per field = 81 max indicators

### Display Size Condition Names

Used with `DSPSIZ` keyword for alternative field locations:

| Display Size | Condition Name |
|--------------|----------------|
| 24x80 (1920 chars) | `*DS3` (default) |
| 27x132 (3564 chars) | `*DS4` |
| User-defined | Custom name on DSPSIZ keyword |

### Data Type / Keyboard Shift (Position 35)

| Entry | Keyboard Shift | Data Type | Notes |
|-------|----------------|-----------|-------|
| Blank | Default | Character/Numeric | Based on decimal positions |
| X | Alphabetic only | Character | A-Z, comma, period, dash, space |
| A | Alphanumeric shift | Character | All characters valid |
| N | Numeric shift | Character or Numeric | Extra position for decimal point |
| S | Signed numeric | Numeric | Display length = program length + 1 |
| Y | Numeric only | Numeric | Digits 0-9, plus, minus, period, comma |
| W | Katakana | Character | Japan only |
| I | Inhibit keyboard | Character or Numeric | No keyboard entry allowed |
| D | Digits only | Character or Numeric | Controllers 6040, 6041, 5294, 5394 |
| M | Numeric only char | Character | 0-9, plus, minus, comma, period, blank |
| F | Floating point | Numeric | 7 extra positions for sign/decimal/exponent |
| L | Date | Date | Length determined by DATFMT |
| T | Time | Time | Format determined by TIMFMT |
| Z | Timestamp | Timestamp | |

### Usage (Position 38)

| Value | Meaning |
|-------|---------|
| I | Input-capable |
| O | Output-only |
| B | Both input and output |
| M | Hidden field (program-to-system) |
| H | Hidden field (system-to-program) |
| P | Program-to-system |

### Location (Positions 39-44)

- **Line**: Positions 39-41 (1-24 or 1-27)
- **Position**: Positions 42-44 (1-80 or 1-132)

**Note**: A field cannot start in position 1 (reserved for attribute character).

---

## DDS Keywords Reference

### File-Level Keywords

#### ALARM (Audible Alarm)
**Level**: Record

Sounds audible alarm when record is displayed. No parameters.

```dds
     A          R CUST
     A                      ALARM
```

---

#### ALIAS (Alternative Name)
**Level**: Field

Specifies an alternative name for a field to be brought into the program.

```dds
     A            FIELDA  25A   1 2ALIAS(CUSTOMERNAME)
```

---

#### ALTHELP (Alternative Help Key)
**Level**: File

Assigns a CA key as alternative Help key.

```dds
     A                  ALTHELP(CA01)
     A                  HELP(01 'HELP KEY PRESSED')
     A          R RECORD
     A            FIELD1  20A   5 5
```

Parameters: `CA01-CA24` (default CA01)

---

#### ALTNAME (Alternative Record Name)
**Level**: Record

Specifies an alternative name for a record.

```dds
     A          R RECORD1
     A                      ALTNAME('ALTREC1')
```

---

#### ALTPAGEDWN / ALTPAGEUP (Alternative Page Down/Up)
**Level**: File

Assigns CF keys as alternative Page keys.

```dds
     A                  ALTPAGEUP
     A                  ALTPAGEDWN
     A          R SUBFILE      SFL
     A            FIELD1  20A   5 5
     A          R CONTROL      SFLCTL(SUBFILE)
     A                      SFLSIZ(30)
     A                      SFLPAG(10)
     A                      SFLDSP
```

Parameters: `CF01-CF24` (defaults: CF07 up, CF08 down)

---

#### ALWGPH (Allow Graphics)
**Level**: File, Record

Allows graphics and alphanumeric to display together on 5292 Model 2.

```dds
     A          R RECORD1    ALWGPH
     A                        23 2'Enter account number:'
     A            ACCT    5  B +2
```

---

#### ALWROL (Allow Roll)
**Level**: Record

Enables program to page through data in a window.

```dds
     A    1  R RECORD1    ALWROL OVERLAY
     A            FLDA    79  I 23 2CHECK(LC)
     A  44                    3 ERRMSG('Record not found' 44)
```

**Note**: Cannot be with ASSUME, KEEP, SFL, SFLCTL, USRDFN

---

#### ASSUME (Assume)
**Level**: Record

System assumes data has not changed; MDT not set.

```dds
     A          R RECORD1    ASSUME
```

---

#### AUTO (Auto)
**Level**: Record

Performs automatic record advance after field exit.

```dds
     A          R RECORD1    AUTO
```

---

#### CHGINPDFT (Change Input Default)
**Level**: Field

Changes input field default attributes.

```dds
     A            FIELD1  10A   1 2CHGINPDFT(*RIGHT *ZBD)
```

---

#### CLEAR (Clear)
**Level**: Record

Clears screen when record is displayed.

```dds
     A          R RECORD1    CLEAR
```

---

#### CLRL (Clear Line)
**Level**: Record

Clears specified line(s) when record format is displayed.

```dds
     A          R RECORD1    CLRL(*ALL)
```

---

#### CSRLOC (Cursor Location)
**Level**: Record

Specifies cursor location on output operation.

```dds
     A          R RECORD1    CSRLOC(LINNBR POSNBR)
     A            LINNBR  3  OH
     A            POSNBR  3  OH
```

---

#### DATE (Date Constant)
**Level**: Field (constant)

Displays current date as constant.

```dds
     A                        1 2DATE(*JOB *YY)
```

Parameters: `*JOB|*SYS`, `*Y|*YY`

---

#### DATFMT (Date Format)
**Level**: Field

Specifies format for date field (data type L).

| Format | Parameter | Length | Example |
|--------|-----------|--------|---------|
| Month/Day/Year | `*MDY` | 8 | 06/21/90 |
| Day/Month/Year | `*DMY` | 8 | 21/06/90 |
| Year/Month/Day | `*YMD` | 8 | 90/06/21 |
| Julian | `*JUL` | 6 | 90/172 |
| ISO | `*ISO` | 10 | 1990-06-21 |
| USA | `*USA` | 10 | 06/21/1990 |
| EUR | `*EUR` | 10 | 21.06.1990 |
| JIS | `*JIS` | 10 | 1990-06-21 |

```dds
     A          R RECORD
     A            DATFLD1 L  B 5 2DATFMT(*JUL)
     A            DATFLD2 L  B 5 22DATFMT(*EUR)
```

---

#### DATSEP (Date Separator)
**Level**: Field

Specifies separator for date field.

Parameters: `/`, `.`, `-`, `,`, `:`

```dds
     A            DATFLD1 L  B 5 2DATFMT(*YMD) DATSEP('/')
```

---

#### DFT (Default)
**Level**: Field (constant)

Specifies constant value (alternative to position 45-80).

```dds
     A                        1 2 DFT('DEFAULT VALUE')
```

---

#### DFTVAL (Default Value)
**Level**: Field

Specifies default value for input-capable field.

```dds
     A            FIELD1  10A   1 2DFTVAL('DEFAULT')
```

---

#### DLTCHK (Delete Check)
**Level**: Field

Deletes CHECK, COMP, RANGE, VALUES, CHKMSGID from referenced field.

```dds
     A            FIELD1  R     1 2DLTCHK
```

---

#### DLTEDT (Delete Edit)
**Level**: Field

Deletes EDTCDE, EDTWRD from referenced field.

```dds
     A            FIELD1  R     1 2DLTEDT
```

---

#### DSPATR (Display Attribute)
**Level**: Field

Specifies display attributes for field.

| Attribute | Meaning |
|-----------|---------|
| BL | Blink |
| CS | Column separator |
| HI | High intensity |
| ND | Nondisplay (hidden) |
| PC | Position cursor |
| PR | Protect |
| RI | Reverse image |
| UL | Underline |
| MDT | Modified data tag on |

```dds
     A            FIELD1  10A   1 2DSPATR(HI RI)
```

---

#### DSPSIZ (Display Size)
**Level**: File

Specifies display device sizes the file can open.

```dds
     A                  DSPSIZ(27 132 *LARGE 24 80 *NORMAL)
```

---

#### DUP (Duplication)
**Level**: Field

Enables Dup key for field duplication.

```dds
     A            FIELD1  10A   1 2DUP
```

---

#### EDTCDE (Edit Code)
**Level**: Field

Edits output for numeric fields.

| Code | Description |
|------|-------------|
| A | X(0)X(0)X |
| B | , (comma) thousand separator |
| C | - (minus) negative |
| D | , (comma), - negative |
| J | / (slash) date edit |
| K | , (comma) and - negative |
| L | / (slash) and - negative |
| M | , (comma) and CR negative |
| N | , (comma) and - negative |
| O | . (period) and - negative |
| P | . (period) thousand separator |
| Q | $ currency symbol |
| R | $ and - negative |
| X | X(0)X(0)X and - negative |
| Y | $ and CR negative |
| Z | Zero suppression |

```dds
     A            FIELD1  5S 0B 5 2EDTCDE(J)
```

---

#### EDTMSK (Edit Mask)
**Level**: Field

Specifies edit mask for numeric output.

```dds
     A            FIELD1  5S 0B 5 2EDTMSK('%,%9')
```

---

#### EDTWRD (Edit Word)
**Level**: Field

Specifies custom edit pattern.

```dds
     A            AMOUNT  7P 2O 1 2EDTWRD(' $ , . 00')
```

Edit word symbols:
- `0` = Digit (shown)
- `9` = Digit (shown)
- ` ` = Space
- `$` = Currency
- `,` = Thousands separator
- `.` = Decimal point
- `-` = Negative sign
- `CR` = Credit notation

---

#### ENTFLDATR (Entry Field Attribute)
**Level**: File, Record, Field

Changes field attribute when cursor is in field.

```dds
     A          R RECORD1    ENTFLDATR(*RED *HI)
```

---

#### ERASE (Erase)
**Level**: Record

Removes record from display.

```dds
     A          R RECORD1    ERASE
```

---

#### ERASEINP (Erase Input)
**Level**: Record

Clears input-capable fields when record displays.

```dds
     A          R RECORD1    ERASEINP(*ALL)
```

---

#### ERRMSG / ERRMSGID (Error Message)
**Level**: Field

Displays error message on message line.

```dds
     A            FIELD1  10A   1 2ERRMSG('Invalid entry' 99)
     A            FIELD2  10A   2 2ERRMSGID(CPF9899 QCPFMSG *LIBL 98)
```

---

#### ERRSFL (Error Subfile)
**Level**: Record

Displays error messages in subfile.

```dds
     A          R CTRL       SFLCTL(SFL)
     A                      ERRSFL
```

---

#### FLTFIXDEC (Floating-Point to Fixed Decimal)
**Level**: File, Record, Field

Converts floating-point to fixed decimal.

```dds
     A          R RECORD1    FLTFIXDEC
```

---

#### FLTPCN (Floating-Point Precision)
**Level**: Field

Specifies floating-point precision.

```dds
     A            FIELD1  9F   1 2FLTPCN(6)
```

---

#### FRCDTA (Force Data)
**Level**: Field

Forces modified data tag on; data sent even if unchanged.

```dds
     A            FIELD1  10A   1 2FRCDTA
```

---

#### GETRETAIN (Get Retain)
**Level**: File, Record

Retains records across multiple writes.

```dds
     A          R RECORD1    GETRETAIN
```

---

#### HELP (Help)
**Level**: File, Record

Enables Help key for record.

```dds
     A          R RECORD1    HELP(01 'HELP PRESSED')
```

---

#### HLPARA (Help Area)
**Level**: Record

Defines help area boundaries.

```dds
     A          H           HLPARA(1 1 12 80)
```

---

#### HLPBDY (Help Boundary)
**Level**: Record

Specifies help text boundaries.

```dds
     A          R RECORD1    HLPBDY(*WINDOW)
```

---

#### HLPCLR (Help Cleared)
**Level**: Record

Clears help when returning.

```dds
     A          R RECORD1    HLPCLR
```

---

#### HLPCMDKEY (Help Command Key)
**Level**: File

Specifies command key to display help.

```dds
     A                  HLPCMDKEY(F1)
```

---

#### HLPDOC (Help Document)
**Level**: Record

Specifies help document name.

```dds
     A          R RECORD1    HLPDOC('MYDOC')
```

---

#### HLPEXCLD (Help Excluded)
**Level**: Field

Excludes field from help.

```dds
     A            FIELD1  10A   1 2HLPEXCLD
```

---

#### HLPFULL (Help Full)
**Level**: Record

Full screen help mode.

```dds
     A          R RECORD1    HLPFULL
```

---

#### HLPID (Help Identifier)
**Level**: Record

Specifies help identifier.

```dds
     A          R RECORD1    HLPID('HELP001')
```

---

#### HLPPNLGRP (Help Panel Group)
**Level**: Record

Specifies help panel group.

```dds
     A          R RECORD1    HLPPNLGRP(PANEL01)
```

---

#### HLPRCD (Help Record)
**Level**: Record

Specifies help record for record.

```dds
     A          H           HLPRCD(RECORD1 FILEA)
```

---

#### HLPRTN (Help Return)
**Level**: Record

Returns to application after help.

```dds
     A          R RECORD1    HLPRTN
```

---

#### HLPSCHIDX (Help Search Index)
**Level**: Record

Specifies help search index.

```dds
     A          R RECORD1    HLPSCHIDX('INDEX1')
```

---

#### HLPSEQ (Help Sequencing)
**Level**: Record

Specifies help sequence.

```dds
     A          R RECORD1    HLPSEQ(*TOP *BOTTOM)
```

---

#### HLPTITLE (Help Title)
**Level**: Record

Specifies help title text.

```dds
     A          R RECORD1    HLPTITLE('Help Title Here')
```

---

#### HOME (Home)
**Level**: Record

Positions cursor at home location.

```dds
     A          R RECORD1    HOME
```

---

#### HTML (Hypertext Markup Language)
**Level**: Record

Specifies HTML content for web enablement.

```dds
     A          R RECORD1    HTML(*INTENSITY)
```

---

#### INDARA (Indicator Area)
**Level**: File

Indicators stored in separate area rather than record.

```dds
     A                  INDARA
```

---

#### INDTXT (Indicator Text)
**Level**: Field

Text displayed with indicator.

```dds
     A            FIELD1  10A   1 2INDTXT('IND01')
```

---

#### INVITE (Invite)
**Level**: Record

Permits record advance without Enter.

```dds
     A          R RECORD1    INVITE
```

---

#### INZINP (Initialize Input)
**Level**: Record

Initializes input-capable fields.

```dds
     A          R RECORD1    INZINP
```

---

#### INZRCD (Initialize Record)
**Level**: Record

Clears record format before display.

```dds
     A          R RECORD1    INZRCD
```

---

#### KEEP (Keep)
**Level**: Record

Prevents record deletion by overlapping records.

```dds
     A          R RECORD1    KEEP
```

---

#### LOCK (Lock)
**Level**: Field

Locks field from changes.

```dds
     A            FIELD1  10A   1 2LOCK
```

---

#### LOGINP (Log Input)
**Level**: Record

Logs input operations.

```dds
     A          R RECORD1    LOGINP
```

---

#### LOGOUT (Log Output)
**Level**: Record

Logs output operations.

```dds
     A          R RECORD1    LOGOUT
```

---

#### LOWER (Lower)
**Level**: Field

Permits lowercase entry.

```dds
     A            FIELD1  10A   1 2LOWER
```

---

#### MAPVAL (Map Values)
**Level**: Field

Maps input values to display values.

```dds
     A            FIELD1  3A   1 2MAPVAL((ABC XYX) (DEF ZYZ))
```

---

#### MDTOFF (Modified Data Tag Off)
**Level**: Field

Turns off MDT; field not sent unless changed.

```dds
     A            FIELD1  10A   1 2MDTOFF
```

---

#### MLTCHCFLD (Multiple-Choice Selection Field)
**Level**: Field

Defines a multiple-choice selection field.

```dds
     A            SELFLD  2Y 0B 3 35MLTCHCFLD
     A                      CHOICE(1 'Option A')
     A                      CHOICE(2 'Option B')
     A                      CHCCTL(1 &CTL1 MSG1112 QUSER/A)
```

---

#### MNUBAR (Menu Bar)
**Level**: Field

Enables menu bar for field.

```dds
     A            SELFLD  2Y 0B 1 2MNUBAR
     A                      MNUBARCHC(1 'File')
     A                      MNUBARCHC(2 'Edit')
```

---

#### MNUBARCHC (Menu-Bar Choice)
**Level**: Field

Specifies menu bar choice.

```dds
     A            SELFLD  2Y 0B 1 2MNUBAR
     A                      MNUBARCHC(1 PULLFILE 'File')
     A                      MNUBARCHC(2 PULLEDIT 'Edit')
```

---

#### MNUBARDSP (Menu-Bar Display)
**Level**: File

Specifies menu bar display attributes.

```dds
     A                  MNUBARDSP(*COLOR GRN)
```

---

#### MNUBARSEP (Menu-Bar Separator)
**Level**: Field

Specifies menu bar separator.

```dds
     A            SELFLD  2Y 0B 1 2MNUBAR
     A                      MNUBARSEP('-')
```

---

#### MNUBARSW (Menu-Bar Switch Key)
**Level**: File

Specifies key to switch menu bars.

```dds
     A                  MNUBARSW(F5)
```

---

#### MNUCNL (Menu-Cancel Key)
**Level**: File

Specifies menu cancel key.

```dds
     A                  MNUCNL(F3)
```

---

#### MOUBTN (Mouse Buttons)
**Level**: File

Enables mouse button functions.

```dds
     A                  MOUBTN(*CLICK1 *CLICK2)
```

---

#### MSGALARM (Message Alarm)
**Level**: Record

Sounds alarm with message.

```dds
     A          R RECORD1    MSGALARM
```

---

#### MSGCON (Message Constant)
**Level**: Field (constant)

Displays message as constant.

```dds
     A                        1 2MSGCON('MSGF' 'MSGID' *LIBL 10)
```

---

#### MSGID (Message Identifier)
**Level**: Field

Associates message ID with field.

```dds
     A            FIELD1  10A   1 2MSGID(CPF9899 QCPFMSG *LIBL)
```

---

#### MSGLOC (Message Location)
**Level**: Record

Specifies message line location.

```dds
     A          R RECORD1    MSGLOC(*NEXTTEXT)
```

---

#### NOCCSID (No Coded Character Set Identifier)
**Level**: Field

Specifies field without CCSID conversion.

```dds
     A            FIELD1  10A   1 2NOCCSID
```

---

#### OPENPRT (Open Printer File)
**Level**: File

Opens printer file for print operations.

```dds
     A                  OPENPRT
```

---

#### OVERLAY (Overlay)
**Level**: Record

Overlays without clearing screen.

```dds
     A          R RECORD1    OVERLAY
```

---

#### OVRATR (Override Attribute)
**Level**: Field

Overrides field attribute.

```dds
     A            FIELD1  10A   1 2OVRATR
```

---

#### OVRDTA (Override Data)
**Level**: Field

Overrides data in field.

```dds
     A            FIELD1  10A   1 2OVRDTA
```

---

#### PAGEDOWN / PAGEUP (Page Down/Up)
**Level**: File, Record

Handles page key functions.

```dds
     A          R CONTROL    SFLCTL(SFL)
     A                      PAGEDOWN
     A                      PAGEUP
```

---

#### PASSRCD (Passed Record)
**Level**: File

Specifies record format passed to another program.

```dds
     A                  PASSRCD(PASSREC)
```

---

#### PRINT (Print)
**Level**: Record

Prints record when displayed.

```dds
     A          R RECORD1    PRINT
```

---

#### PROTECT (Protect)
**Level**: Field

Protects field from input.

```dds
     A            FIELD1  10A   1 2PROTECT
```

---

#### PSHBTNCHC (Push Button Field Choice)
**Level**: Field

Defines push button field choice.

```dds
     A            BTN1    2Y 0B 1 2PSHBTNFLD
     A                      PSHBTNCHC(1 'Action 1' CF01)
```

---

#### PSHBTNFLD (Push Button Field)
**Level**: Field

Defines push button field.

```dds
     A            BTN1    2Y 0B 1 2PSHBTNFLD
     A                      PSHBTNCHC(1 'Action 1' CF01)
```

---

#### PULLDOWN (Pull-Down Menu)
**Level**: Field

Enables pull-down menu.

```dds
     A            SELFLD  2Y 0B 1 2PULLDOWN(*SLSIND)
```

---

#### PUTOVR (Put with Explicit Override)
**Level**: Record

Overrides and puts data.

```dds
     A          R RECORD1    PUTOVR
```

---

#### PUTRETAIN (Put-Retain)
**Level**: Record

Retains field contents on display.

```dds
     A          R RECORD1    PUTRETAIN
```

---

#### RANGE (Range)
**Level**: Field

Validates input within range.

```dds
     A            FIELD1  5S 0B 1 2RANGE(0 100)
```

---

#### REF (Reference)
**Level**: File

Specifies file for field references.

```dds
     A                  REF(PAYROLL)
```

---

#### REFFLD (Referenced Field)
**Level**: Field

References field from another file/record.

```dds
     A            FIELD1  R     1 2REFFLD(FLD001 PAYROLL)
```

---

#### RETLCKSTS (Retain Lock Status)
**Level**: Record

Retains field lock status.

```dds
     A          R RECORD1    RETLCKSTS
```

---

#### RETKEY / RETCMDKEY (Retain Function/Command Keys)
**Level**: File

Retains specified function keys.

```dds
     A                  RETKEY(*ALL)
     A                  RETCMDKEY(*ALL)
```

---

#### RMVWDW (Remove Window)
**Level**: Record

Removes window from display.

```dds
     A          R RECORD1    RMVWDW
```

---

#### RTNCSRLOC (Return Cursor Location)
**Level**: Record

Returns cursor location to program.

```dds
     A          R RECORD1    RTNCSRLOC
```

---

#### RTNDTA (Return Data)
**Level**: Field

Returns field data in feedback area.

```dds
     A            FIELD1  10A   1 2RTNDTA
```

---

#### SETOF / SETOFF (Set Off)
**Level**: Field

Sets response indicator off.

```dds
     A            FIELD1  10A   1 2SETOF(50)
```

---

## Subfile Keywords

### SFL (Subfile)
**Level**: Record

Defines record format as subfile.

```dds
     A          R SFLRCD      SFL
     A*
     A          R SFLCTL      SFLCTL(SFLRCD)
     A                      SFLPAG(17)
     A                      SFLSIZ(17)
     A                      SFLDSP
     A                      SFLDSPCTL
```

**Required Keywords for Subfile Control**:
- SFLCTL
- SFLDSP
- SFLPAG
- SFLSIZ

---

### SFLCTL (Subfile Control)
**Level**: Record

Defines subfile-control record format.

```dds
     A          R SFLCTL      SFLCTL(SFLRCD)
     A                      SFLPAG(17)
     A                      SFLSIZ(17)
     A                      SFLDSP
```

---

### SFLSIZ (Subfile Size)
**Level**: Record

Number of records in subfile.

```dds
     A                      SFLSIZ(50)
```

---

### SFLPAG (Subfile Page)
**Level**: Record

Number of records per page.

```dds
     A                      SFLPAG(10)
```

---

### SFLDSP (Subfile Display)
**Level**: Record

Displays subfile records.

```dds
     A                      SFLDSP
```

---

### SFLDSPCTL (Subfile Display Control)
**Level**: Record

Required for input to subfile control.

```dds
     A                      SFLDSPCTL
```

---

### SFLCLR (Subfile Clear)
**Level**: Record

Clears subfile.

```dds
     A                      SFLCLR
```

---

### SFLDLT (Subfile Delete)
**Level**: Record

Deletes subfile records.

```dds
     A  90                  SFLDLT
```

---

### SFLDROP (Subfile Drop)
**Level**: Record

Allows subfile fold/drop.

```dds
     A                      SFLDROP
```

---

### SFLEND (Subfile End)
**Level**: Record

Shows end indicator.

```dds
     A                      SFLEND(*MORE)
```

---

### SFLENTER (Subfile Enter)
**Level**: Record

Handles Enter key.

```dds
     A  99                  SFLENTER
```

---

### SFLFOLD (Subfile Fold)
**Level**: Record

Folds long records.

```dds
     A                      SFLFOLD
```

---

### SFLINZ (Subfile Initialize)
**Level**: Record

Initializes subfile.

```dds
     A                      SFLINZ
```

---

### SFLLIN (Subfile Line)
**Level**: Record

Specifies subfile line.

```dds
     A                      SFLLIN(*NO)
```

---

### SFLMLTCHC (Subfile Multiple Choice)
**Level**: Record

Multiple choice selection list.

```dds
     A          R SFLCTL      SFLCTL(SFLRCD)
     A                      SFLMLTCHC
     A                      CHCAVAIL(*DSPATR HI)
```

---

### SFLMODE (Subfile Mode)
**Level**: Record

Specifies subfile mode.

```dds
     A                      SFLMODE(*SNGCHC)
```

---

### SFLMSG / SFLMSGID (Subfile Message)
**Level**: Record

Displays message in subfile.

```dds
     A                      SFLMSG('Message text')
     A                      SFLMSGID(CPF9899 QCPFMSG)
```

---

### SFLMSGKEY (Subfile Message Key)
**Level**: Field

Message key for subfile.

```dds
     A            MSGKEY  4A  P
```

---

### SFLMSGRCD (Subfile Message Record)
**Level**: Record

Message subfile record.

```dds
     A          R SFLRCD      SFL
     A            MSGKEY  4A  P
     A            MSGDTA  80A  O
     A          R SFLCTL      SFLCTL(SFLRCD)
     A                      SFLMSGRCD
     A                      SFLPGMQ(*EXT)
```

---

### SFLNXTCHG (Subfile Next Changed)
**Level**: Field

Indicates next changed record.

```dds
     A            FIELD1  10A   5 5SFLNXTCHG
```

---

### SFLPAG (Subfile Page)
See SFLPAG above.

---

### SFLPGMQ (Subfile Program Message Queue)
**Level**: Field

Specifies message queue.

```dds
     A            PGMQ   10A  P
```

---

### SFLRCDNBR (Subfile Record Number)
**Level**: Record

Specifies record number field.

```dds
     A                      SFLRCDNBR(CURSOR)
```

---

### SFLRNA (Subfile Records Not Active)
**Level**: Record

Handles inactive records.

```dds
     A                      SFLRNA
```

---

### SFLROLVAL (Subfile Roll Value)
**Level**: Record

Specifies roll value.

```dds
     A                      SFLROLVAL(*SFLPAG)
```

---

### SFLRTNSEL (Subfile Return Selected)
**Level**: Record

Returns selected choices.

```dds
     A                      SFLRTNSEL
```

---

### SFLSCROLL (Subfile Scroll)
**Level**: Record

Specifies scroll amount.

```dds
     A                      SFLSCROLL(*CURSOR)
```

---

### SFLSNGCHC (Subfile Single Choice)
**Level**: Record

Single choice selection list.

```dds
     A          R SFLCTL      SFLCTL(SFLRCD)
     A                      SFLSNGCHC
```

---

### SFLCSRPRG (Subfile Cursor Progression)
**Level**: Record

Cursor progression in subfile.

```dds
     A                      SFLCSRPRG
```

---

### SFLCSRRRN (Subfile Cursor RRN)
**Level**: Field

Returns cursor relative record number.

```dds
     A            RRN    5Y 0H
```

---

### SFLCHCCTL (Subfile Choice Control)
**Level**: Field

Controls choice availability.

```dds
     A            F1    2Y 0B 3 35SNGCHCFLD
     A                      SFLCHCCTL
```

---

### SFLCSRRRN (Subfile Cursor Relative Record Number)
**Level**: Field

Returns relative record number.

```dds
     A            RRN    5Y 0H
```

---

### SFLROLVAL (Subfile Roll Value)
**Level**: Record

Specifies page滚动值。

```dds
     A                      SFLROLVAL(5)
```

---

## Window Keywords

### WINDOW (Window)
**Level**: Record

Defines window on display.

```dds
     A          R RECORD1    WINDOW(5 10 10 60)
```

Parameters: `(start_line start_pos height width)`

---

### WDWBORDER (Window Border)
**Level**: File, Record

Specifies window border.

```dds
     A          R RECORD1    WDWBORDER(*BOX)
     A                      WDWTITLE('Window Title')
```

Parameters: `*BOX | *DOUBLE | *SINGLE | *NONE`

---

### WDWTITLE (Window Title)
**Level**: Record

Specifies window title.

```dds
     A          R RECORD1    WDWTITLE('Title Text')
```

---

---

## Selection Field Keywords

### CHOICE (Selection Field Choice)
**Level**: Field

Defines choice for selection field.

```dds
     A            SELFLD  2Y 0B 1 2SNGCHCFLD
     A                      CHOICE(1 '>Option A')
     A                      CHOICE(2 '>Option B')
```

---

### SNGCHCFLD (Single-Choice Selection Field)
**Level**: Field

Defines single-choice selection field.

```dds
     A            SELFLD  2Y 0B 1 2SNGCHCFLD
     A                      CHOICE(1 'Option A')
```

---

### CHCACCEL (Choice Accelerator Text)
**Level**: Field

Accelerator text for choice.

```dds
     A                      CHCACCEL(1 'F4')
```

---

### CHCAVAIL (Choice Color/Attribute Available)
**Level**: Field

Choice color when available.

```dds
     A                      CHCAVAIL(*DSPATR HI)
```

---

### CHCSLT (Choice Color/Attribute Selected)
**Level**: Field

Choice color when selected.

```dds
     A                      CHCSLT((*COLOR RED) (*DSPATR RI))
```

---

### CHCUNAVAIL (Choice Color/Attribute Unavailable)
**Level**: Field

Choice color when unavailable.

```dds
     A                      CHCUNAVAIL(*DSPATR ND)
```

---

### CHCCTL (Choice Control)
**Level**: Field

Controls choice availability.

```dds
     A            F1    2Y 0B 3 35SNGCHCFLD
     A                      CHCCTL(1 &CTL1 MSG1112 QUSER/A)
```

---

## Validation Keywords

### CHECK (Check)
**Level**: Field

Performs validity checking.

| Code | Description |
|------|-------------|
| AB | Auto-validate blanks |
| LC | Check lowercase |
| ME | Mandatory entry |
| MN | Mandatory numeric |
| M10 | Match (equal) |
| M11 | Match (not equal) |
| M12 | Match (greater) |
| RE | Required entry |
| RL | Record-level CHECK |
| RZ | Right-aligned zero-fill |
| VN | Valid signed numeric |
| ZV | Zero validation |

```dds
     A            FIELD1  10A   1 2CHECK(LC)
     A            FIELD2  5S 0B 2 2CHECK(ME)
     A            FIELD3  5S 0B 3 2CHECK(RANGE(0 100))
```

---

### CHKMSGID (Check Message Identifier)
**Level**: Field

Custom error message for validity check.

```dds
     A            FIELD1  5S 0B 1 2CHECK(RANGE(0 100))
     A                      CHKMSGID(CPF9899 QCPFMSG *LIBL)
```

---

### COMP / CMP (Comparison)
**Level**: Field

Validates against comparison value.

| Operator | Meaning |
|----------|---------|
| EQ | Equal |
| NE | Not equal |
| LT | Less than |
| NL | Not less than |
| GT | Greater than |
| NG | Not greater than |
| LE | Less than or equal |
| GE | Greater than or equal |

```dds
     A            FIELD1  3A   1 2COMP(EQ 'ABC')
     A            FIELD2  6S 0B 2 2COMP(GT +021920)
```

---

### VALUES (Values)
**Level**: Field

Validates against list of values.

```dds
     A            FIELD1  10A   1 2VALUES('A' 'B' 'C')
```

---

### VALNUM (Validate Numeric)
**Level**: Field

Validates numeric field.

```dds
     A            FIELD1  5S 0B 1 2VALNUM
```

---

## System Field Keywords

### SYSNAME (System Name)
**Level**: Field (constant)

Displays system name.

```dds
     A                        1 70SYSNAME
```

---

### TIME (Time Constant)
**Level**: Field (constant)

Displays current time.

```dds
     A                        1 2TIME(*JOB)
```

---

### TIMFMT (Time Format)
**Level**: Field

Specifies time format.

| Format | Parameter | Length | Example |
|--------|-----------|--------|---------|
| USA | `*USA` | 8 | 10:30 AM |
| ISO | `*ISO` | 8 | 10:30:00 |
| EUR | `*EUR` | 8 | 10.30.00 |
| JIS | `*JIS` | 8 | 10:30:00 |

```dds
     A            TIMFLD T  B 5 2TIMFMT(*EUR)
```

---

### TIMSEP (Time Separator)
**Level**: Field

Specifies time separator.

```dds
     A            TIMFLD T  B 5 2TIMFMT(*ISO) TIMSEP(':')
```

---

### USER (User)
**Level**: Field (constant)

Displays current user name.

```dds
     A                        1 70USER
```

---

### TEXT (Text)
**Level**: Field

Describes field purpose.

```dds
     A            FIELD1  10A   1 2TEXT('Customer name')
```

---

## Color Values

| Color | Value | Notes |
|-------|-------|-------|
| Blue | BLU | |
| Green | GRN | Default for fields |
| Pink | PNK | |
| Red | RED | |
| Turquoise | TRQ | |
| Yellow | YLW | |
| White | WHT | Default for selected |

---

## Display Attributes (DSPATR)

| Attr | Description | Notes |
|------|-------------|-------|
| BL | Blink | Not on all displays |
| CS | Column separator | 5292, 3179, 3197, 3477, 3486, 3487, 3488 |
| HI | High intensity | |
| ND | Nondisplay | Hidden field |
| PC | Position cursor | |
| PR | Protect | Prevents input |
| RI | Reverse image | |
| UL | Underline | |
| MDT | Modified data tag on | Forces data transmission |

---

## Keyboard Shift Reference

| Entry | Name | Valid Input |
|-------|------|-------------|
| X | Alphabetic only | A-Z, comma, period, dash, space |
| A | Alphanumeric | All characters |
| N | Numeric shift | All characters |
| S | Signed numeric | 0-9 only (extra position for sign) |
| Y | Numeric only | 0-9, plus, minus, period, comma |
| W | Katakana | Katakana characters (Japan) |
| I | Inhibit | No keyboard entry |
| D | Digits only | 0-9 (6040/6041/5294/5394 controllers) |
| M | Numeric char | 0-9, plus, minus, comma, period, blank |
| F | Floating point | Scientific notation |

---

## Special Characters in Edit Words (EDTWRD)

| Symbol | Display |
|--------|---------|
| 0 | Digit (shown) |
| 9 | Digit (shown) |
| (space) | Space |
| $ | Currency symbol |
| , | Thousands separator |
| . | Decimal point |
| - | Negative sign |
| + | Positive sign |
| CR | Credit notation |
| ( ) | Negative parentheses |

---

## DDS Coding Example

```dds
     A* DISPLAY FILE EXAMPLE
     A
     A                  REF(PAYROLL)
     A          R MENU
     A          H           HLPARA(1 1 12 80)
     A                      HLPRCD(RECORD1 FILEA)
     A N01
     A O 02    FLDA    20I 2O   2 2DSPATR(HI)
     A            FLDB    22N 2B   3 2
     A  72 73
     A O 60 61 62
     A A  63             DSPATR(HI)
     A            FLDC    7Y 0B   7 20DSPATR(RI PC)
     A  42 43
     A O 60 61
     A O  62              9 2'Constant'
     A            FLDD R       11 2
```

---

## Notes

- File name is specified via CRTDSPF command, not in DDS
- Maximum record formats: **1024**
- Maximum fields per record: **32,763**
- Maximum fields displayed per record: **4095**
- Maximum combined length of named fields: **32,763 bytes**
- Display device maximum: 24x80 or 27x132 depending on DSPSIZ

---

*Source: IBM i 7.2 Programming - DDS for Display Files (© Copyright IBM 1999, 2013)*