---
title: DDS Printer File Keywords Reference
description: Comprehensive reference for IBM i DDS (Data Description Specifications) printer file keywords
source: /home/admin/wiki/raw/papers/dds-printer-files.txt
lines: 8023
keywords_count: ~80 unique keywords
---

# DDS Printer File Keywords Reference

IBM i DDS for printer files defines how data is formatted and printed using the Create Printer File (CRTPRTF), Change Printer File (CHGPRTF), and Override Printer File (OVRPRTF) commands.

## Table of Contents

1. [Record-Level Keywords](#record-level-keywords)
2. [Field-Level Keywords](#field-level-keywords)
3. [File-Level Keywords](#file-level-keywords)
4. [AFP Resources](#afp-resources)
5. [Barcode Types](#barcode-types)
6. [Color Models](#color-models)
7. [Date/Time Formats](#datetime-formats)
8. [Edit Codes](#edit-codes)
9. [Font Keywords](#font-keywords)
10. [Page Layout (LINE/SPACE/SKIP)](#page-layout-linespaceskip)
11. [DBCS Keywords](#dbcs-keywords)

---

## Record-Level Keywords

Record-level keywords apply to an entire record format.

| Keyword | Description |
|---------|-------------|
| `BOX` | Print a rectangle |
| `DRAWER` | Specify paper drawer |
| `DUPLEX` | Duplex printing control |
| `ENDPAGE` | Eject current page after record prints |
| `ENDPAGGRP` | End a logical grouping of pages |
| `FLTPCN` | Specify floating-point precision (*SINGLE/*DOUBLE) |
| `FONT` | Specify font ID for printing |
| `FONTNAME` | Specify TrueType font name |
| `FORCE` | Force new sheet for duplex printing |
| `GDF` | Print a graphic data file |
| `HIGHLIGHT` | Print field in bold |
| `INVMMAP` | Call a new medium map |
| `INVDTAMAP` | Specify data map for formatted page |
| `LINE` | Print horizontal or vertical line |
| `LPI` | Change lines per inch (4/6/8/9/12) |
| `OUTBIN` | Specify output bin |
| `OVERLAY` | Print an overlay |
| `PAGRTT` | Specify page rotation (0/90/180/270) |
| `PAGSEG` | Print a page segment |
| `POSITION` | Define field location on page |
| `PRTQLTY` | Vary print quality (*STD/*DRAFT/*NLQ/*FASTDRAFT) |
| `SKIPA` | Skip to line after printing |
| `SKIPB` | Skip to line before printing |
| `SPACEA` | Space lines after printing |
| `SPACEB` | Space lines before printing |
| `STAPLE` | Perform stapling operation |
| `STRPAGGRP` | Begin logical grouping of pages |
| `ZFOLD` | Perform z-fold operation |

---

## Field-Level Keywords

Field-level keywords apply to specific fields within records.

| Keyword | Description |
|---------|-------------|
| `BARCODE` | Print barcode |
| `CCSID` | Specify UTF-16/UCS-2 coded character set |
| `CHRID` | Specify character identifier |
| `CHRSIZ` | Expand character width/height (1 or 2) |
| `COLOR` | Specify print color |
| `CPI` | Specify characters per inch |
| `CVTDTA` | Convert character data to hexadecimal |
| `DATE` | Print current system date |
| `DATFMT` | Specify date format |
| `DATSEP` | Specify date separator |
| `DFNCHR` | Define custom characters |
| `DFT` | Specify constant/default value |
| `DLTEDT` | Delete editing |
| `DOCIDXTAG` | Specify document index tag |
| `EDTCDE` | Specify edit code |
| `EDTWRD` | Specify edit word |
| `FLTFIXDEC` | Print floating-point as fixed decimal |
| `FNTCHRSET` | Specify font character set |
| `INDTXT` | Associate descriptive text with indicator |
| `MSGCON` | Specify message constant |
| `PAGNBR` | Print page number |
| `POSITION` | Define field location |
| `TIME` | Print current system time |
| `TIMFMT` | Specify time format |
| `TIMSEP` | Specify time separator |
| `TRNSPY` | Prevent SCS control interpretation |
| `TXTRTT` | Rotate text (0/90/180/270) |
| `UNDERLINE` | Underline the field |
| `UNISCRIPT` | Control Unicode text layout |

---

## File-Level Keywords

File-level keywords apply to the entire printer file.

| Keyword | Description |
|---------|-------------|
| `AFPRSC` | Specify AFP resource library |
| `ALIAS` | Specify alternate record format name |
| `BLKFOLD` | Allow text block folding |
| `DTASTMCMD` | Data stream command |
| `INDARA` | Remove indicators to separate area |
| `REF` | Reference file for field descriptions |
| `RELPOS` | Relative positioning for +n fields |

---

## Page Layout: LINE/SPACE/SKIP

### LINE Keyword
Prints horizontal or vertical lines.

```
LINE(position-down position-across line-length direction line-width [line-pad] [color])
```

**Parameters:**
- `position-down/position-across`: 0 to 57.790 cm from margins
- `line-length`: 0.001 to 57.790 cm
- `direction`: `*HRZ` (horizontal) or `*VRT` (vertical)
- `line-width`: Width with special values `*NARROW` (12/1440 in), `*MEDIUM` (24/1440 in), `*WIDE` (36/1440 in)
- `line-pad`: `*TOP/*BOT` for horizontal, `*LEFT/*RIGHT` for vertical

### SKIPA/SKIPB (Skip After/Before)
```
SKIPA(skip-after-line-number)   | 1-255
SKIPB(skip-before-line-number)   | 1-255
```

### SPACEA/SPACEB (Space After/Before)
```
SPACEA(space-after-value)   | 0-255
SPACEB(space-before-value)  | 0-255
```

**Processing Order:**
```
SKIPB → SPACEB → [STAPLE/OUTBIN/ZFOLD] → SPACEA → SKIPA
```

---

## Font Keywords

### FONT
```
FONT(font-identifier [(*POINTSIZE height-value [width-value])])
```

- Numeric font ID (up to 10 digits) or graphic font name
- `*VECTOR` for vector fonts on 4234 IPDS printer
- Point size: 0.1 to 999.9

### FONTNAME
```
FONTNAME('font-name' [(*POINTSIZE h [w])] [(*ROTATION deg)] 
         [(*CODEPAGE [lib/]code-page)] [(*IGCCODEPAGE [lib/]igc-code-page)])
```

- TrueType/OpenType font name up to 125 characters
- Valid for DEVTYPE(*AFPDS)

### FNTCHRSET
```
FNTCHRSET([lib/]font-character-set [lib/]code-page 
           [(*POINTSIZE height-value width-value)])
```

- Font character set (8 chars) + code page (8 chars)
- IBM-supplied: C0* font character sets, T1* code pages
- Valid for DEVTYPE(*AFPDS)

### CDEFNT (Coded Font Name)
```
CDEFNT([lib/]coded-font-name [(*POINTSIZE h w)])
```

---

## AFP Resources

### AFPRSC (AFP Resource)
```
AFPRSC(library-name)
```
Specifies library containing AFP resources.

### OVERLAY
```
OVERLAY([lib/]overlay-name position-down position-across [(*ROTATION deg)])
```
- Max 10 overlays per page
- Valid for DEVTYPE(*AFPDS)

### PAGSEG (Page Segment)
```
PAGSEG([lib/]page-segment-name position-down position-across 
        [(*SIZE height width)] [(*ROTATION deg)])
```
- Max 10 page segments per page
- Valid for DEVTYPE(*AFPDS)

### GDF (Graphic Data File)
```
GDF(lib/file member position-down position-across depth width rotation)
```
Prints IBM Graphic Object Content Architecture (GOCA) charts.

---

## Barcode Types

### BARCODE Keyword
```
BARCODE(barcode-type height [check-digit] [print-position] 
        [HRG-spacing] [*COLOR color] [*CDTSA] [*CNTDTY])
```

### Supported Barcode Types

| Type | Description | Valid Heights |
|------|-------------|---------------|
| `*CODE128` | Code 128 | 2-99 |
| `*CODE128A` | Code 128 Set A | 2-99 |
| `*CODE128B` | Code 128 Set B | 2-99 |
| `*CODE128C` | Code 128 Set C | 2-99 |
| `*CODE39` | Code 39 | 2-99 |
| `*CODE93` | Code 93 | 2-99 |
| `*EAN13` | EAN-13 | 5-99 |
| `*EAN8` | EAN-8 | 5-99 |
| `*PDF417` | PDF417 (2D) | 2-99 |
| `*QRCODE` | QR Code (2D) | 2-99 |
| `*DATAMTX` | Data Matrix (2D) | 2-99 |
| `*MAXICODE` | Maxicode (2D) | 2-99 |
| `*UPCE` | UPC-E | 5-99 |
| `*US4S` | US Planet Code | 5-99 |
| `*US5S` | US Postnet | 5-99 |
| `*NW7` | Codabar/NW7 | 2-99 |
| `*3OF9` | Code 3 of 9 | 2-99 |

### 2D Barcode Parameters

**PDF417:**
```
BARCODE(*PDF417 height [rows [columns]] [*CDV] [*CDTA])
```
- Rows: 3-90 (default: auto)
- Columns: 1-30 (default: auto)

**QR Code:**
```
BARCODE(*QRCODE height [model [error-level [mode]]])
```
- Model: *AUTO, *M1, *M2
- Error level: *L(7%), *M(15%), *Q(25%), *H(30%)

**Data Matrix:**
```
BARCODE(*DATAMTX height [format [aspect-ratio]])
```

**Maxicode:**
```
BARCODE(*MAXICODE height [mode [class [country [postal-code [message-id]]]]])
```

---

## Color Models

### COLOR Keyword
```
COLOR([*COLOR color-name] | [*RGB r g b] | [*CMYK c m y k] | 
       [*CIELAB l c1 c2] | [*HIGHLIGHT h])
```

### COLOR Parameters

| Model | Syntax | Values |
|-------|--------|--------|
| Named | `(*COLOR color-name)` | `*BLACK`, `*BLUE`, `*GREEN`, `*PINK`, `*RED`, `*YELLOW`, `*CYAN`, `*WHITE`, `*DEFAULT` |
| RGB | `(*RGB red green blue)` | 0-255 each |
| CMYK | `(*CMYK cyan magenta yellow black)` | 0-100 each |
| CIELAB | `(*CIELAB l c1 c2)` | L: 0-100, C1/C2: -128 to 127 |
| Highlight | `(*HIGHLIGHT h coverage)` | h: 1-5, coverage: 0-100 |

---

## Date/Time Formats

### DATFMT (Date Format)
```
DATFMT(*JOB | *MDY | *DMY | *YMD | *JUL | *ISO | *USA | *EUR | *JIS)
```

| Format | Example Output |
|--------|----------------|
| `*JOB` | Job default |
| `*MDY` | 12/31/99 |
| `*DMY` | 31/12/99 |
| `*YMD` | 99/12/31 |
| `*JUL` | 99/365 |
| `*ISO` | 1999-12-31 |
| `*USA` | 12/31/1999 |
| `*EUR` | 31.12.1999 |
| `*JIS` | 1999-12-31 |

### DATSEP (Date Separator)
```
DATSEP(*JOB | 'separator-char')
```
- Default separators: `/` (-), `.` (*ISO/*EUR/*JIS), `,` (*USA)

### TIMFMT (Time Format)
```
TIMFMT(*HMS | *ISO | *USA | *EUR | *JIS)
```

| Format | Example Output |
|--------|----------------|
| `*HMS` | 14:00:00 |
| `*ISO` | 14.00.00 |
| `*USA` | 02:00 PM |
| `*EUR` | 14.00.00 |
| `*JIS` | 14:00:00 |

### TIMSEP (Time Separator)
```
TIMSEP(*JOB | 'separator-char')
```
- Valid: `:`, `.`, `,`, blank

---

## Edit Codes

### EDTCDE (Edit Code)
```
EDTCDE(edit-code)
```

| Code | Description | Example |
|------|-------------|---------|
| `1` | Suppress leading zeros, show decimal | `12.34` |
| `2` | Commas, flush right, zero fill | `1,234.00` |
| `3` | Asterisk fill, float dollar | `***1234.56` |
| `4` | Slash date format | `12/31/99` |
| `A` | Suppress if zero, credit indicated | `12.34CR` |
| `B` | Suppress if zero | `12.34` |
| `C` | Commas, flush right, zero fill, credit | `1,234.56-` |
| `D` | Float minus sign, zero suppressed | `12.34-` |
| `J` | Suppress if zero, leading $ | `$12.34` |
| `K` | Suppress if zero | `12.34` |
| `L` | Float $ left | `$  12.34` |
| `M` | Float $ left, credit | `$  12.34CR` |
| `N` | Float $ left | `$12.34` |
| `O` | Float $ left, credit | `$12.34CR` |
| `Q` | Float $ right | `12.34  $` |
| `W` | Suppress leading zeros | `12.34` |
| `Y` | YMD date edit | `99/12/31` |
| `Z` | Suppress all, asterisks | `****`**` |

### EDTWRD (Edit Word)
```
EDTWRD('edit-word')
```

**Special Characters:**
- `&` = Suppress and blank
- `_` = Force blank
- `-` = Float minus sign
- `+` = Float plus sign
- `$` = Float dollar sign
- `,` = Comma
- `.` = Period
- `0-9` = Fixed digit

---

## DBCS Keywords

### CCSID (Coded Character Set Identifier)
```
CCSID(UTF16-CCSID [&field] | *REFC [*CONVERT | *NOCONVERT] [alt-field-len])
```

- Valid CCSIDs: 1200 (UTF-16), 13488 (UCS-2)
- Use `*NOCONVERT` for Unicode printing with TrueType fonts

### DFNLIN (Define Line)
```
DFNLIN(*HRZ | *VRT start-line start-position length)
```
- Horizontal: draws at bottom of character spaces
- Vertical: draws on right edge of character spaces

### IGCALTTYP (Alternative Data Type)
- Changes alphanumeric fields to DBCS (type O) when IGCDTA(*YES)

### IGCANKCNV (Alphanumeric-to-DBCS Conversion)
- Converts alphanumeric to DBCS characters (Japanese)
- Adds shift-control characters (0E/0F)

### IGCCDEFNT (DBCS Coded Font)
```
IGCCDEFNT([lib/]coded-font [(*POINTSIZE h w)])
```
- IBM-supplied: X0* coded fonts

### IGCCHRRTT (DBCS Character Rotation)
- Rotates DBCS characters 90 degrees counterclockwise
- For 5553 printers and IPDS AFP(*YES) printers

---

## DDS Syntax Quick Reference

### DDS Coding Form Positions

| Positions | Content |
|-----------|---------|
| 1 | Option indicator |
| 2-5 | Record name |
| 6 | Record type (R) |
| 7-18 | Reserved |
| 19-28 | Field name |
| 29 | Reference (R) |
| 30-34 | Field length |
| 35 | Data type |
| 36-37 | Decimal positions |
| 38 | Reserved |
| 39-41 | Line number |
| 42-44 | Position |
| 45-80 | Keywords |

### Data Types

| Type | Description |
|------|-------------|
| `A` | Alphanumeric |
| `S` | Zoned decimal |
| `P` | Packed decimal |
| `F` | Floating-point |
| `O` | DBCS-open |
| `G` | DBCS-graphic |
| `T` | Time |
| `L` | Date |

### DEVTYPE Options

| Type | Description |
|------|-------------|
| `*SCS` | SNA Character Stream |
| `*IPDS` | Intelligent Printer Data Stream |
| `*AFPDS` | Advanced Function Printing Data Stream |
| `*LINE` | Line data |
| `*AFPDSLINE` | AFP line data |

---

## Common Keyword Restrictions

### Cannot combine with POSITION:
- SPACEA, SPACEB, SKIPA, SKIPB
- BOX, LINE, OVERLAY, PAGSEG, GDF, ENDPAGE

### Cannot combine with CPI/LPI/DFNCHR:
- BLKFOLD, PRTQLTY

### Valid for DEVTYPE(*AFPDS) only:
- AFPRSC, ENDPAGE, ENDPAGGRP, FNTCHRSET, FONTNAME, FORCE
- GDF, INVMMAP, INVDTAMAP, LINE, OUTBIN, OVERLAY
- PAGSEG, POSITION, PAGRTT, RELPOS, STAPLE, STRPAGGRP
- TXTRTT, UNISCRIPT, ZFOLD

---

## Examples

### Basic Printer File
```
     A          R RECORD1
     A            FLD1           5A    16 01
     A            FLD2           7S 2       DATFMT(*ISO)
     A  01                                  HIGHLIGHT
```

### Barcode Example
```
     A            BARCD         13A    10  5BARCODE(*CODE128 10)
     A                                      *COLOR(*BLUE)
```

### Overlay Example
```
     A          R HEADER
     A                                      OVERLAY(MYLIB/HEADER 0 0)
     A            TITLE         30A    10  1
```

### Page Segment with Position
```
     A          R LOGO
     A                                      PAGSEG(MYLIB/LOGO 0.0 3.0)
     A            TITLE         30A    5  1
```
