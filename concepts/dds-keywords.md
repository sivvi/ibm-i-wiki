---
title: DDS Keywords - Shared Keywords
description: Data Description Specifications keywords that apply to both physical and logical files in IBM i DDS
---

# DDS Keywords - Shared Keywords

DDS keywords that apply to both physical and logical files.

## Keyword Reference

| Keyword | Description |
|---------|-------------|
| ABSVAL | Absolute value key sequencing |
| ALIAS | Alternative field name |
| ALTSEQ | Alternative collating sequence |
| COMP | Comparison for validity checking or select/omit |
| DATFMT | Date format specification |
| DATSEP | Date separator character |
| DESCEND | Descending key sequence |
| FCFO | First-changed first-out record order |
| FIFO | First-in first-out record order |
| FORMAT | Share field specs from another format |
| LIFO | Last-in first-out record order |
| NOALTSEQ | Override ALTSEQ for specific key field |
| RANGE | Value range checking |
| TEXT | Text description for record or field |
| TIMFMT | Time format specification |
| TIMSEP | Time separator character |
| VALUES | Allowed values for validity checking |
| CMP | Comparison (equivalent to COMP) |

---

## ABSVAL (Absolute Value)

**Applies to:** Key field level

**Description:** Ignore the sign of numeric field when sequencing values.

**Syntax:** No parameters

**Effect:** Uses absolute value of negative fields for key sequencing.

**Example (Records with zoned decimal key field):**
| Record | Key Field | Hex | With ABSVAL Sequence |
|--------|-----------|-----|---------------------|
| 2 | 00 | F0D0 | First |
| 5 | 20 | F2F0 | Second |
| 4 | 97 | F9F7 | Third |
| 1 | 98 | F9F8 | Fourth |
| 3 | 98- | F9D8 | Fifth |
| 6 | 99 | F9D9 | Sixth |

**Restrictions:**
- Not valid for character, date, time, timestamp, or hexadecimal fields
- Cannot be used with DIGIT, SIGNED, UNSIGNED, or ZONE
- Causes ALTSEQ (file-level) to be ignored for this key field
- NOALTSEQ is automatically in effect when ABSVAL specified

**Example:**
```dds
00010A            ORDAMT         5  0
00020A          K ORDAMT                    ABSVAL
```

---

## ALIAS (Alternative Name)

**Applies to:** Field level

**Description:** Specifies an alternative name for a field for use by high-level language compilers.

**Syntax:**
```dds
ALIAS(alternative-name)
```

**Parameters:** Alternative name (must differ from all other ALIAS names and DDS field names in record format)

**Rules:**
- Alternative name cannot be used within DDS
- Alternative name cannot be used as key field name, REFFLD parameter, or in CPYF command
- When field with ALIAS is referenced, ALIAS keyword is copied unless explicitly specified

**Example:**
```dds
00010A            FIELDA        25A         ALIAS(CUSTOMERNAME)
```

---

## ALTSEQ (Alternative Collating Sequence)

**Applies to:** File level

**Description:** Use an alternative collating sequence table when sequencing records.

**Syntax:**
```dds
ALTSEQ([library-name/]table-name)
```

**Parameters:** Table name (required), optional library name (defaults to *LIBL)

**Rules:**
- Table must be created with CRTTBL command
- User must have use authority to the table

**Restrictions:**
- Not valid when FILETYPE(*SRC) specified
- Not valid when key fields are packed decimal, binary, or floating-point
- Not valid when key fields use ABSVAL or SIGNED (NOALTSEQ assumed)
- Not valid when SRTSEQ(*NONE) not specified on CRTPF/CRTLF
- Cannot be specified with REFACCPTH

**Effect:** Causes zoned key fields to default to unsigned sequence

**Example:**
```dds
00010A                                      ALTSEQ(TABLELIB/TABLE1)
00020A          R RECORD1                   PFILE(PF1)
00030A            :
00070A            NAME          20
00090A          K NAME
```
Records are sequenced by NAME using alternative collating sequence TABLE1.

---

## COMP (Comparison)

**Applies to:** Field level, Select/omit field level

**Description:** Validity checking for fields (when referred from display files) or select/omit records (in logical files).

**Field Level Syntax:**
```dds
COMP(relational-operator value)
```

**Select/Omit Level Syntax:**
```dds
COMP(relational-operator value)
COMP(relational-operator field-name)
COMP(relational-operator *NULL)
```

**Relational Operators:**
| Operator | Meaning |
|----------|---------|
| EQ | Equal to |
| NE | Not equal to |
| LT | Less than |
| NL | Not less than |
| GT | Greater than |
| NG | Not greater than |
| LE | Less than or equal to |
| GE | Greater than or equal to |

**At Field Level:**
- Does not affect the physical or logical file
- Copied to display file when field is referenced
- Cannot specify field name as parameter value
- Cannot specify *NULL as parameter value
- Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

**At Select/Omit Level:**
- Selects or omits records based on comparison
- Value parameter: character constant, hexadecimal string, numeric string, date, time, or timestamp constant
- Field name parameter: compares to another field value
- *NULL: compares to null value (EQ or NE only)
- Binary character fields must have same actual lengths for comparison

**Example:**
```dds
00010A          R RECORD                    PFILE(PF1)
00020A            FIELDA         1  0       COMP(NE O)
00030A            FIELDB         1          COMP(NE 'A')
00040A          K FIELDB
00050A          S FIELDC                    COMP(EQ FIELDD)
00060A          S FIELDA                    COMP(NE O)
00070A          S FIELDE                    COMP(NE *NULL)
00080A          O FIELDB                    COMP(GE 'A')
```

---

## CMP (Comparison)

**Applies to:** Field level, Select/omit field level

**Description:** Equivalent to COMP keyword. COMP is preferred.

**Syntax:**
```dds
CMP(relational-operator value)
```

---

## DATFMT (Date Format)

**Applies to:** Field level

**Description:** Specifies the format of a date field.

**Syntax:**
```dds
DATFMT(date-format)
```

**Date Formats:**

| Format Name | Parameter | Separator | Field Length | Example |
|-------------|-----------|-----------|--------------|---------|
| Job Default | *JOB | / | 8 | 06/21/90 |
| Month/Day/Year | *MDY | / | 8 | 06/21/90 |
| Day/Month/Year | *DMY | / | 8 | 21/06/90 |
| Year/Month/Day | *YMD | / | 8 | 90/06/21 |
| Julian | *JUL | / | 6 | 90/172 |
| ISO | *ISO | - | 10 | 1990-06-21 |
| USA | *USA | / | 10 | 06/21/1990 |
| European | *EUR | . | 10 | 21.06.1990 |
| JIS | *JIS | - | 10 | 1990-06-21 |

**Rules:**
- If not specified, default is *ISO
- Field length and decimal positions must be blank for date fields
- If format is *JOB, *MDY, *DMY, *YMD, or *JUL and field allows null, valid date required on DFT

**Example:**
```dds
00030A            DATFLD2         L         DATFMT(*DMY) DATSEP('-')
```

---

## DATSEP (Date Separator)

**Applies to:** Field level

**Description:** Specifies the separator character for date fields.

**Syntax:**
```dds
DATSEP(*JOB | 'date-separator')
```

**Parameters:**
- `*JOB`: Use job attribute (default)
- `date-separator`: Valid values are : (colon), . (period), , (comma), or blank

**Rules:**
- Cannot be specified with *ISO, *USA, *EUR, or *JIS date formats (fixed separators)
- If not specified for physical file, defaults to job attribute
- If not specified for logical file, defaults to separator from physical file

**Example:**
```dds
00030A            DATFLD2         L         DATFMT(*DMY) DATSEP('-')
00040A            DATFLD4         L         DATSEP(' ')
```

---

## DESCEND (Descend)

**Applies to:** Key field level

**Description:** Specifies that key field values are retrieved in descending sequence.

**Syntax:** No parameters

**Default:** Ascending sequence

**Example:**
```dds
00010A          K ITEM
00020A          K BALDUE                    DESCEND
```

---

## FCFO (First-Changed First-Out)

**Applies to:** File level

**Description:** Records with duplicate key values are retrieved in first-changed first-out order.

**Syntax:** No parameters

**Rules:**
- At least one key field must be specified
- Not valid when FILETYPE(*SRC) specified
- Records ordered by time key value changed (not relative record number)

**Restrictions:** Cannot be used with FIFO, LIFO, UNIQUE, or REFACCPTH

**Example:**
```dds
00010A                                      FCFO
00020A          R CUSREC                    TEXT('CUSTOMER RECORD')
00030A            CUSNAMEF      10A
00040A            CUSNAMEM       1A
00050A            CUSNAMEL      10A
00060A          K CUSNAMEL
```

---

## FIFO (First-In First-Out)

**Applies to:** File level

**Description:** Records with duplicate key values are retrieved in first-in first-out order.

**Syntax:** No parameters

**Rules:**
- At least one key field must be specified
- Not valid when FILETYPE(*SRC) specified
- Records ordered by relative record number

**Restrictions:** Cannot be used with FCFO, LIFO, UNIQUE, or REFACCPTH

**Example:**
```dds
00010A                                      FIFO
00020A          R CUSREC                    TEXT('CUSTOMER RECORD')
00030A            CUSNAMEF      10A
00040A            CUSNAMEM       1A
00050A            CUSNAMEL      10A
00060A          K CUSNAMEL
```

---

## FORMAT (Format)

**Applies to:** Record level

**Description:** Specifies that this record format shares field specifications from a previously defined format.

**Syntax:**
```dds
FORMAT([library-name/]database-file-name)
```

**Parameters:**
- `database-file-name`: Required - name of file with previously defined format
- `library-name`: Optional - defaults to *LIBL

**Rules:**
- Record format name must match the previously defined format name
- Cannot specify field specifications when FORMAT used
- Can specify key and select/omit specifications
- Not valid in join logical files
- Cannot specify DDM file

**Example:**
```dds
00010A          R RECORD                    PFILE(FILE2)
00020A                                      FORMAT(FILE1)
```

---

## LIFO (Last-In First-Out)

**Applies to:** File level

**Description:** Records with duplicate key values are retrieved in last-in first-out order.

**Syntax:** No parameters

**Rules:**
- At least one key field must be specified
- Not valid when FILETYPE(*SRC) specified
- Records ordered by relative record number

**Restrictions:** Cannot be used with FCFO, FIFO, UNIQUE, or REFACCPTH

**Example:**
```dds
00010A                                      LIFO
00020A          R CUSREC                    TEXT('CUSTOMER RECORD')
00030A            CUSNAMEF      10A
00040A            CUSNAMEM       1A
00050A            CUSNAMEL      10A
00060A          K CUSNAMEL
```

---

## NOALTSEQ (No Alternative Collating Sequence)

**Applies to:** Key field level

**Description:** Specifies that ALTSEQ (file-level) does not apply to this key field.

**Syntax:** No parameters

**Rules:**
- If ABSVAL or SIGNED is specified, NOALTSEQ is automatically in effect

**Example:**
```dds
00010A                                      ALTSEQ(TABLELIB/TABLE1)
00020A          R DSTR
00050A            CODE           1
00060A            NAME          20
00090A          K CODE
00100A          K NAME                      NOALTSEQ
```
CODE is sequenced by alternative collating sequence. NAME is sequenced by EBCDIC.

---

## RANGE (Range)

**Applies to:** Field level, Select/omit field level

**Description:** Specifies valid range of values for a field or to select/omit records.

**Syntax:**
```dds
RANGE(low-value high-value)
```

**At Field Level:**
- Validity checking for display files
- Does not affect physical or logical file
- Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

**At Select/omit Level:**
- Selects records where field value is within range
- Character: character strings or X'hex' strings
- Numeric: numeric strings (no quotes)
- Date: valid date in DATFMT format
- Time: valid time in TIMFMT format
- Timestamp: 'YYYY-MM-DD-HH.MM.SS.UUUUUU'

**Example:**
```dds
00010A          R RECORD                    PFILE(PF1)
00020A            FIELDA         1  0       RANGE(2 5)
00030A            FIELDB         1          RANGE('2' '5')
00040A          S FIELDA                    RANGE(1   4)
```
```dds
00050A          K FLD1
00060A          S CODEA                     RANGE(X'51' X'54')
```

---

## TEXT (Text)

**Applies to:** Record level, Field level

**Description:** Provides a text description for documentation purposes.

**Syntax:**
```dds
TEXT('description')
```

**Parameters:** Text string enclosed in single quotation marks (max 50 characters used by compilers)

**Example:**
```dds
00010A          R CUSMST                    TEXT('Customer Master Record')
00020A            FLD1           3  0       TEXT('ORDER NUMBER FIELD')
```

---

## TIMFMT (Time Format)

**Applies to:** Field level

**Description:** Specifies the format of a time field.

**Syntax:**
```dds
TIMFMT(time-format)
```

**Time Formats:**

| Format Name | Parameter | Field Length | Example |
|-------------|-----------|--------------|---------|
| HMS | *HMS | 8 | 14:00:00 |
| ISO | *ISO | 8 | 14.00.00 |
| USA | *USA | 8 | 2:00 PM |
| European | *EUR | 8 | 14.00.00 |
| JIS | *JIS | 8 | 14:00:00 |

**Rules:**
- If not specified for physical file, default is *ISO
- If not specified for logical file, defaults to format from physical file
- Cannot specify TIMSEP with *ISO, *USA, *EUR, or *JIS (fixed separators)

**Example:**
```dds
00020A          R RECORD
00030A            TIMFLD1         T         TIMFMT(*ISO)
00040A            TIMFLD2         T         TIMFMT(*USA)
```

---

## TIMSEP (Time Separator)

**Applies to:** Field level

**Description:** Specifies the separator character for time fields.

**Syntax:**
```dds
TIMSEP(*JOB | 'time-separator')
```

**Parameters:**
- `*JOB`: Use job attribute (default)
- `time-separator`: Valid values are : (colon), . (period), , (comma), or blank

**Rules:**
- Cannot be specified with *ISO, *USA, *EUR, or *JIS time formats (fixed separators)
- If not specified for physical file, defaults to job attribute
- If not specified for logical file, defaults to separator from physical file

**Example:**
```dds
00030A            TIMFLD1         T         TIMSEP(' ')
00040A            TIMFLD2         T         TIMSEP('.')
```

---

## VALUES (Values)

**Applies to:** Field level, Select/omit field level

**Description:** Specifies allowed values for a field or to select/omit records.

**Syntax:**
```dds
VALUES(value-1 [value-2...[value-100]])
```

**At Field Level:**
- Validity checking for display files
- Up to 100 values allowed
- Not valid on floating-point (F), hexadecimal (H), date (L), time (T), or timestamp (Z) fields

**At Select/omit Level:**
- Selects records matching specified values
- Character: character strings or X'hex' strings
- Numeric: numeric strings (no quotes)
- Date: valid date in DATFMT format
- Time: valid time in TIMFMT format
- Timestamp: 'YYYY-MM-DD-HH.MM.SS.UUUUUU'

**Example:**
```dds
00010A          R RECORD1                   PFILE(PF1)
00020A            FIELDA         1  0       VALUES(1 6 9)
00030A            FIELDB         1          VALUES('A' 'B' 'C')
00050A          S FIELDB                    VALUES('A' 'B')
00060A          S FIELDA                    VALUES(1 6)
```
```dds
00060A          S CODEA                     VALUES(X'51' X'54' X'AE')
```

---

## Key Field Sequencing Keywords

These keywords affect how numeric key fields are sequenced:

| Keyword | Effect |
|---------|--------|
| ABSVAL | Use absolute value (ignore sign) |
| DIGIT | Use only digit portion (4 bits) of each byte |
| SIGNED | Consider sign (default for zoned decimal) |
| UNSIGNED | Treat as unsigned binary data |
| ZONE | Use only zone portion (4 bits) of each byte |
| DESCEND | Retrieve in descending sequence |
| NOALTSEQ | Override file-level ALTSEQ for this field |

**Mutually Exclusive Key Sequencing Keywords:**
- ABSVAL, DIGIT, SIGNED, UNSIGNED, ZONE are mutually exclusive
- Each affects how the numeric field value is interpreted for sequencing

---

## Record Duplicate Handling Keywords

These keywords define the order for retrieving records with duplicate key values:

| Keyword | Order |
|---------|-------|
| FIFO | First-in first-out (by relative record number) |
| LIFO | Last-in first-out (by relative record number) |
| FCFO | First-changed first-out (by time key changed) |

**Note:** If none specified, order is not guaranteed. UNIQUE prevents duplicates entirely.

---

## Validity Checking Keywords

These keywords provide validity checking when fields are referenced in display files:

| Keyword | Purpose |
|---------|---------|
| CHECK | Validity check codes (AB, ME, MF, M10, M11, VN, VNE) |
| CHKMSGID | Custom error message ID |
| CMP | Comparison (equivalent to COMP) |
| COMP | Comparison operators |
| RANGE | Range of values |
| VALUES | Allowed values list |

---

## Data Type Conversion Summary

When concatenating fields, the resulting data type is determined by:

| Fields Included | Result Type |
|-----------------|--------------|
| Any hexadecimal (H) | H |
| Any character (A) without H | A |
| Only numeric (S, P, B) | S (zoned decimal) |
| UTF-8 fields | UTF-8 |
| UTF-16 fields present | UTF-16 |
| Otherwise UCS-2 fields | UCS-2 |
| Binary character fields | Binary character |

---

## DDS Syntax Notation Example

```dds
     A* Example of physical file DDS
     A                                      REF(INVENCTL/INVENTORY)
     A                                      UNIQUE
     A          R ORDFMT                    TEXT('Format for Purchase Orders')
     A            ORDNBR         7  0       COLHDG('Order' 'Number')
     A            ITMNBR    R   10
     A            SUPNBR    R   +2          REFFLD(SUPID SUPLIB/SUPMST)
     A            QTYORD         5B
     A          K ORDNBR
     A          K ITMNBR                    ABSVAL
```

Position 6 must be A (form type). Keywords appear in positions 45-80.
