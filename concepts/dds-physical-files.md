---
title: DDS Keywords - Physical Files Only
description: Data Description Specifications keywords specific to physical files in IBM i DDS
---

# DDS Keywords for Physical Files

Physical file DDS keywords that apply only to physical files (not logical files).

## Keyword Reference

| Keyword | Description |
|---------|-------------|
| ALWNULL | Allow null values for a field |
| CCSID | Specify coded character set identifier |
| CHECK | Validity checking for display file fields |
| CHKMSGID | Specify error message for validity checking |
| COLHDG | Column heading for field labeling |
| DFT | Specify default value for a field |
| DIGIT | Use digit portion only of key field |
| EDTCDE | Edit code for field display formatting |
| EDTWRD | Edit word for custom field display |
| FLTPCN | Floating-point precision (single/double) |
| REF | Reference file for field definitions |
| REFACCPTH | Reference access path definition |
| REFFLD | Reference a specific field |
| REFSHIFT | Reference shift attribute |
| SIGNED | Consider sign in key sequencing |
| TRNTBL | Translation table for character fields |
| UNSIGNED | Unsigned numeric key sequencing |
| VARLEN | Variable-length field definition |
| ZONE | Use zone portion only of key field |

---

## ALWNULL (Allow Null Value)

**Applies to:** Field level

**Description:** Defines a field to allow the null value.

**Syntax:**
```dds
A fieldname   nA   ALWNULL
```

**Parameters:** None

**Restrictions:**
- Maximum field length is 32,765 bytes (32,739 if variable length)
- When DATFMT(*JOB, *MDY, *DMY, *YMD, or *JUL) is specified with null value support, a valid date must be specified on DFT keyword

**Example:**
```dds
00010A          R RECORD1
00020A            FIELD1        75A         ALWNULL
00030A            FIELD2       100A
00040A            FIELD3          L         ALWNULL
00050A                                      DATFMT(*MDY)
00060A                                      DFT('12/25/93')
```

---

## CCSID (Coded Character Set Identifier)

**Applies to:** File level, Field level

**Syntax:**
```dds
CCSID(value [field-display-length | *MIN | *LEN display-positions]
      [*CONVERT | *NOCONVERT] [*NORMALIZE])
```

**Parameters:**
- `value`: 1-5 digit CCSID number
- `field-display-length`: Optional display length for UCS-2/UTF-16
- `*CONVERT/*NOCONVERT`: Conversion control for printer files
- `*NORMALIZE`: UTF-8/UTF-16 normalization

**Example:**
```dds
00010A                                      CCSID(285)
00020A          R RECORD1
00030A            FIELD1        75G         CCSID(13488)
00040A            FIELD2       150A
00050A            FIELD4        10A         CCSID(1208 *NORMALIZE)
```

---

## CHECK (Check)

**Applies to:** Field level

**Description:** Validity checking for display file fields. Does not affect physical/logical files.

**Syntax:**
```dds
CHECK(edit-check-code [. . .])
```

**Valid Codes:**
- `AB` - Allow blank
- `ME` - Mandatory enter
- `MF` - Mandatory fill
- `M10` - Modulus 10 self-check
- `M11` - Modulus 11 self-check
- `VN` - Validate name
- `VNE` - Validate name extended

**Restrictions:** Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

---

## CHKMSGID (Check Message Identifier)

**Applies to:** Field level

**Syntax:**
```dds
CHKMSGID(message-id [library/]message-file [message-data-field])
```

**Restrictions:** Allowed only on fields with VALUES, RANGE, CMP, COMP, CHECK(M10), CHECK(M11), CHECK(VN), or CHECK(VNE)

---

## COLHDG (Column Heading)

**Applies to:** Field level

**Syntax:**
```dds
COLHDG('line-1' ['line-2' ['line-3']])
```

**Parameters:** Up to 3 lines of 20 characters each

**Example:**
```dds
00150A            ORDDAT         5  0       COLHDG('Order' 'Date')
00160A            NAME          20          COLHDG('Customer''s Name')
00170A            CITY          20          COLHDG('Customer' 'City' 'Field')
```

---

## DFT (Default)

**Applies to:** Field level

**Syntax:**
```dds
DFT('value' | numeric-value | X'hexadecimal-value' | *NULL)
```

**Parameters:**
- Character constant, hexadecimal value, *NULL, or numeric value
- Date fields: valid date in DATFMT format
- Time fields: valid time in TIMFMT format
- Timestamp: 'YYYY-MM-DD-HH.MM.SS.UUUUUU'

**Example:**
```dds
00010A          R RECORD1
00020A            CHARFLD1      20A         DFT('Sample field')
00030A            CHARFLD2       5A         DFT(X'D985955185')
00040A            HEXFLD1        3H         DFT('ABC')
00050A            NUMFLD1        5S 0       DFT(99999)
```

---

## DIGIT (Digit)

**Applies to:** Key field level

**Description:** Use only the digit portion (farthest right 4 bits) of each byte when sequencing key field.

**Syntax:** No parameters

**Parameters:** None

**Restrictions:**
- Valid only for character, hexadecimal, or zoned decimal fields
- Cannot be used with ABSVAL, SIGNED, or ZONE keywords

**Example:**
```dds
00040A          K ORDTYP                    DIGIT
```

---

## EDTCDE (Edit Code)

**Applies to:** Field level

**Syntax:**
```dds
EDTCDE(edit-code [* | floating-currency-symbol])
```

**Restrictions:** Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

**Example:**
```dds
     A            PRICE          5  2       EDTCDE(J)
     A            SALES          7  2       EDTCDE(K $)
     A            SALARY         8  2       EDTCDE(1 *)
```

---

## EDTWRD (Edit Word)

**Applies to:** Field level

**Syntax:**
```dds
EDTWRD('edit-word')
```

**Restrictions:** Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

**Example:**
```dds
     A            BALANCE        7  2       EDTWRD('$    0.  &CR')
```

---

## FLTPCN (Floating-Point Precision)

**Applies to:** Field level

**Syntax:**
```dds
FLTPCN(*SINGLE | *DOUBLE)
```

**Parameters:**
- `*SINGLE`: Single precision (up to 9 digits)
- `*DOUBLE`: Double precision (up to 17 digits)

**Example:**
```dds
00090A            FIELDA        17F 4       FLTPCN(*DOUBLE)
```

---

## REF (Reference)

**Applies to:** File level

**Syntax:**
```dds
REF([library-name/]database-file-name [record-format-name])
```

**Example:**
```dds
00010A                                      REF(FILE1)
00020A          R RECORD
00030A            FLD1      R
```

---

## REFFLD (Referenced Field)

**Applies to:** Field level

**Syntax:**
```dds
REFFLD([record-format-name/]referenced-field-name
      [{*SRC | [library-name/]database-file-name}])
```

**Parameters:**
- Referenced field name (required)
- Optional record format name
- Optional *SRC or library/file reference

**Example:**
```dds
00010A          R FMAT1
00020A            ITEM           5
00030A            SUPDESC       20      R   REFFLD(SUPDESC SUPFILE)
```

---

## REFSHIFT (Reference Shift)

**Applies to:** Field level

**Syntax:**
```dds
REFSHIFT(keyboard-shift)
```

**Valid Values:** A-Z, N, X, or blank

**Example:**
```dds
00010A          R RECORD
00020A            FIELDA         5          REFSHIFT(X)
00030A            FIELDN         4P         REFSHIFT(N)
```

---

## SIGNED (Signed)

**Applies to:** Key field level

**Description:** Consider sign of numeric values when sequencing (default behavior for zoned decimal).

**Syntax:** No parameters

**Parameters:** None

**Restrictions:**
- Not valid for character, date, time, timestamp, or hexadecimal fields
- Cannot be used with ABSVAL, DIGIT, UNSIGNED, or ZONE

**Example:**
```dds
00010A          R RECORD
00020A            FLDA           7S 2
00030A            FLDB
00040A          K FLDA                      SIGNED
```

---

## TRNTBL (Translation Table)

**Applies to:** Field level

**Syntax:**
```dds
TRNTBL([library-name/]translation-table-name)
```

**Restrictions:**
- Field must be character type
- Cannot be used with date, time, or timestamp fields
- ICU tables not allowed

**Example:**
```dds
00010A          R RECORD1                   PFILE(PF1)
00020A            CHAR1              I      TRNTBL(LIB1/TBL1)
00030A            CHAR2           A  I      TRNTBL(LIB2/TBL2)
```

---

## UNSIGNED (Unsigned)

**Applies to:** Key field level

**Description:** Sequence numeric key field as unsigned binary data.

**Syntax:** No parameters

**Parameters:** None

**Restrictions:** Cannot be used with ABSVAL, DIGIT, SIGNED, or ZONE

---

## VARLEN (Variable-Length Field)

**Applies to:** Field level

**Syntax:**
```dds
VARLEN[(allocated-length)]
```

**Parameters:**
- Optional allocated length (1 to max field length)
- Default: data stored in variable portion

**Restrictions:**
- Valid only for character and graphic fields
- Maximum length 32,740 (32,739 if null allowed)
- Cannot be used with date, time, or timestamp fields

**Example:**
```dds
00010A          R RECORD1
00020A            FIELD1       100A         VARLEN(30)
00030A            FIELD2       200A         VARLEN
```

---

## ZONE (Zone)

**Applies to:** Key field level

**Description:** Use only the zone portion (farthest left 4 bits) of each byte when sequencing key field.

**Syntax:** No parameters

**Parameters:** None

**Restrictions:**
- Valid only for character, hexadecimal, or zoned decimal fields
- Cannot be used with ABSVAL, SIGNED, or DIGIT

**Example:**
```dds
00010A          K CODE                      ZONE
```

---

## Positional Entries Summary (Positions 1-44)

| Position(s) | Description |
|-------------|-------------|
| 1-5 | Sequence number (optional, documentation only) |
| 6 | Form type - must be A for DDS |
| 7 | Comment - asterisk (*) identifies comment line |
| 8-16 | Condition (leave blank for physical/logical files) |
| 17 | Name type: R=Record, K=Key field, Blank=Field |
| 18 | Reserved |
| 19-28 | Name (record format, field, or key field name) |
| 29 | Reference (R required for referenced fields) |
| 30-34 | Field length |
| 35 | Data type (P, S, A, H, L, T, Z, etc.) |
| 36-37 | Decimal positions |
| 38 | Usage (I=input-only, B=both, N=neither) |
| 39-44 | Field location in record |
| 45-80 | Keywords |

---

## Data Types

| Code | Data Type |
|------|-----------|
| A | Character |
| B | Binary |
| F | Floating-point |
| G | Graphic (DBCS) |
| H | Hexadecimal |
| L | Date |
| P | Packed decimal |
| S | Zoned decimal |
| T | Time |
| Z | Timestamp |

---

## Access Path Types

### Arrival Sequence Access Path
- No key fields specified
- Records retrieved in physical order
- Cannot specify select/omit unless DYNSLT keyword used

### Keyed Sequence Access Path
- One or more key fields specified
- Records retrieved in key sequence
- Select/omit fields allowed

### Reference Access Path (REFACCPTH)
- Copies access path from another file
- Key, select, omit information copied

---

## Record Format Naming

Physical files contain only one record format. The format name can be:
1. Specified in DDS with field definitions
2. Referenced from another file using FORMAT keyword

**Example:**
```dds
00010A* PHYSICAL FILE CODING EXAMPLE
00020A                                      REF(INVENCTL/INVENTORY)
00030A                                      UNIQUE
00040A          R ORDFMT                    TEXT('Format for Purchase Orders')
00050A            ORDNBR         7  0       COLHDG('Order' 'Number')
00060A            ITMNBR    R   10
00070A            SUPNBR    R   +2          REFFLD(SUPID SUPLIB/SUPMST)
00080A            QTYORD         5B
00090A          K ORDNBR
00100A          K ITMNBR                    ABSVAL
```
