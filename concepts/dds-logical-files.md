---
title: DDS Keywords - Logical Files Only
description: Data Description Specifications keywords specific to logical files in IBM i DDS
---

# DDS Keywords for Logical Files

Logical file DDS keywords that apply only to logical files (not physical files).

## Keyword Reference

| Keyword | Description |
|---------|-------------|
| ALL | Select/omit after all other rules processed |
| CONCAT | Concatenate multiple fields into one |
| DYNSLT | Dynamic select/omit at processing time |
| JDFTVAL | Provide defaults for missing join records |
| JDUPSEQ | Order of duplicate join field records |
| JFILE | Identify physical files for join |
| JFLD | Join field pairs |
| JOIN | Identify file pair for join |
| JREF | Identify physical file for field |
| PFILE | Identify physical file reference |
| RENAME | Rename physical file field |
| SST | Substring of existing field |
| TRNTBL | Translation table for character fields |
| UNIQUE | No duplicate key values allowed |

---

## ALL (All)

**Applies to:** Select/omit field level

**Description:** Specifies the action to take after all other select/omit specifications have been processed.

**Syntax:** No parameters

**Usage:**
- `S` in position 17: Select all records not meeting other rules
- `O` in position 17: Omit all records not meeting other rules

**Rules:**
- Must follow other select/omit keywords
- No field name specified with ALL
- If not specified, default action is opposite of last specification

**Example:**
```dds
00010A          S ACT                       COMP(EQ 3000)
00020A          S ACT                       COMP(GT 3100)
00030A          O AMT                       COMP(LT 0)
00040A          O                           ALL
```

---

## CONCAT (Concatenate)

**Applies to:** Field level

**Description:** Combines two or more fields from the physical file into one field in the logical file.

**Syntax:**
```dds
CONCAT(field-1 field-2...)
```

**Parameters:** Two or more physical file field names, separated by blanks

**Rules:**
- Field names specified in order to be concatenated
- Cannot include floating-point, date, time, or timestamp fields
- Cannot include fields with decimal positions (except zero)
- Concatenated field must be input-only (I) if result is variable-length, null-allowed, UCS-2, UTF-16, UTF-8, or binary character
- In join logical files, all fields must be from same physical file

**Data Type Determination:**
- Contains H fields → hexadecimal (H)
- Contains A fields (no H) → character (A)
- Contains only numeric (S, P, B) → zoned decimal (S)
- Contains UTF-8 → UTF-8
- Contains UCS-2/UTF-16 → UTF-16 (if any UTF-16) or UCS-2
- Contains binary character → binary character

**Length Limits:**
- Zoned decimal (S): max 63 bytes
- Character/Hexadecimal: max 32,766 bytes
- Variable length: max 32,740 (32,739 if null allowed)

**Example:**
```dds
00010A          R RECORD1                   PFILE(PF1)
00020A            DATE                      CONCAT(MTH DAY YEAR)
```

```dds
00010A          R RECORD3                   PFILE(PF1)
00020A            DATE                      CONCAT(MTH DAY YEAR)
00030A            CMPDAT                    CONCAT(DAY MTH YEAR)
```

---

## DYNSLT (Dynamic Select)

**Applies to:** File level

**Description:** Specifies that select/omit tests are done at processing time rather than building them into the access path.

**Syntax:** No parameters

**Benefits:**
- More efficient for files read occasionally
- More efficient when physical files are updated frequently
- More efficient for files with high percentage of selected records

**Trade-off:** Slower I/O performance due to testing each record at retrieval

**Requirements:**
- Required for arrival sequence files with select/omit
- Required for join logical files with JDFTVAL
- Required when select/omit fields from multiple physical files are on same statement or mixed select/omit statements

**Restrictions:** Cannot be specified with REFACCPTH keyword

**Example:**
```dds
00010A                                      DYNSLT
00020A          R RECORD1                   PFILE(PF1)
00030A            FLD1
00040A            FLD2
00050A          S FLD1                      COMP(GT 2)
```

---

## JDFTVAL (Join Default Values)

**Applies to:** File level

**Description:** Provides default values for fields when a join to a secondary file does not produce records.

**Syntax:** No parameters

**Default Values:**
- Blanks for character and hexadecimal fields
- Zeros for numeric fields

**Behavior:**
- With JDFTVAL: records returned even if secondary file has no match
- Without JDFTVAL: records without match in secondary file are skipped

**Example:**
```dds
00010A                                      JDFTVAL
00020A          R RECORD1                   JFILE(PF1 PF2)
00030A          J                           JOIN(PF1 PF2)
00040A                                      JFLD(NAME NAME)
00050A            NAME                      JREF(1)
00060A            ADDR
00070A            BAL
```

---

## JDUPSEQ (Join Duplicate Sequence)

**Applies to:** Join level

**Syntax:**
```dds
JDUPSEQ(sequencing-field-name [*DESCEND])
```

**Parameters:**
- `sequencing-field-name`: Field in "to" file, not a join field
- `*DESCEND`: Optional, reverses order

**Rules:**
- Total length of JFLD to-fields plus JDUPSEQ fields cannot exceed 120 bytes
- Field can be concatenated (CONCAT) or substring (SST)
- Need not be in record format
- Only affects order when duplicates exist

**Example:**
```dds
00010A          R JREC                      JFILE(PF1 PF2)
00020A          J                           JOIN(PF1 PF2)
00030A                                      JFLD(NAME1 NAME2)
00040A                                      JDUPSEQ(PHONE)
00050A            NAME1
00060A            ADDR
00070A            PHONE
```

---

## JFILE (Joined Files)

**Applies to:** Record level

**Description:** Identifies physical files containing data for a join logical file.

**Syntax:**
```dds
JFILE([library-name/]physical-file-name [..256])
```

**Parameters:**
- Minimum 2 physical file names required
- Up to 256 files total (first is primary, rest are secondary)
- Same file can be specified multiple times

**Rules:**
- First file is primary (join starts here)
- Files with fewer records should be specified toward left
- Not allowed with PFILE keyword
- DDM files allowed only when creating on remote system

**Example:**
```dds
00010A          R JREC                      JFILE(PF1 PF2)
00020A          J                           JOIN(PF1 PF2)
00030A                                      JFLD(NAME1 NAME2)
```

---

## JFLD (Joined Fields)

**Applies to:** Join level

**Description:** Identifies from and to fields used to join physical files.

**Syntax:**
```dds
JFLD(from-field-name to-field-name)
```

**Rules:**
- At least one JFLD required per join specification
- Fields must have same attributes (length, data type, decimal positions)
- Character fields need not have same length (shorter is padded with blanks)
- Total length of JFLD to-fields plus JDUPSEQ fields cannot exceed 120 bytes
- Binary character fields can only join with binary character fields

**Example:**
```dds
00010A          R JREC                      JFILE(PF1 PF2)
00020A          J                           JOIN(PF1 PF2)
00030A                                      JFLD(NAME1 NAME2)
```

---

## JOIN (Join)

**Applies to:** Join level

**Description:** Identifies which pair of files are joined by a join specification.

**Syntax:**
```dds
JOIN(from-file to-file)
```

**Parameters:**
- File names or relative file numbers (1-255)
- From-file number must be less than to-file number

**Rules:**
- Optional when only two files on JFILE
- Required when more than two files on JFILE
- Each secondary file can be a to-file only once
- Optional when files appear only once on JFILE

**Example:**
```dds
00010A          R RECORD1                   JFILE(PFA PFB PFC)
00020A          J                           JOIN(PFA PFB)
00030A                                      JFLD(NAME1 NAME2)
00040A          J                           JOIN(PFA PFC)
00050A                                      JFLD(NAME1 NAME3)
```

---

## JREF (Join Reference)

**Applies to:** Field level

**Description:** Identifies which physical file contains a field (for fields with duplicate names across files).

**Syntax:**
```dds
JREF(file-name | relative-file-number)
```

**Rules:**
- Required when field name exists in multiple physical files
- Optional when field name is unique among JFILE files
- Required on all fields when JFILE specifies same file twice

**Example:**
```dds
00010A          R JOINREC                   JFILE(PFA PFB PFC)
00050A            NAME                      JREF(PFB)
```
```dds
00010A          R JOINREC                   JFILE(PFA PFB PFC)
00050A            NAME                      JREF(2)
```

---

## PFILE (Physical File)

**Applies to:** Record level

**Description:** Identifies physical files containing data for a simple or multiple-format logical file.

**Syntax:**
```dds
PFILE([library-name/]physical-file-name [.32])
```

**Parameters:**
- Up to 32 physical file names per logical file
- Optional library name (defaults to *LIBL)

**Rules:**
- Required for every record format in simple/multiple-format logical files
- Not allowed with JFILE keyword
- All fields in record format must exist in all specified physical files
- Cannot be used to bring fields from separate physical files into one record format

**Example:**
```dds
00010A          R LOGRCD1                   PFILE(PF1)
```

---

## RENAME (Rename)

**Applies to:** Field level

**Description:** Specifies that a logical file field name differs from its physical file field name.

**Syntax:**
```dds
RENAME(physical-file-field-name)
```

**Parameters:** Name of field in physical file record format

**Rules:**
- One physical field can be renamed to multiple logical fields
- If same physical field specified multiple times (via RENAME or CONCAT), last occurrence determines update order

**Example:**
```dds
00050A          R RCD1                      PFILE(PF1)
00060A            QTY                       RENAME(QTYDUE)
```

```dds
00050A          R RCD2                      PFILE(PF2)
00130A            QTY                       RENAME(QTYDUE)
00140A          K QTY
```

---

## SST (Substring)

**Applies to:** Field level

**Description:** Specifies a character string that is a subset of an existing field.

**Syntax:**
```dds
SST(field-name starting-position [length])
```

**Parameters:**
- `field-name`: Source field (must be defined earlier or in physical file)
- `starting-position`: Required, 1-based position
- `length`: Optional (field length used if not specified)

**Rules:**
- Resulting field usage must be I (input-only) or N (neither)
- Cannot be used with CONCAT, RENAME, or TRNTBL on same field
- Source field cannot have CONCAT, TRNTBL, or SST keywords
- Starting position and length must be positive, substring must not exceed source field length

**Data Type Result:**
| Source Type | Result Type |
|-------------|------------|
| A | A |
| H | H |
| S | A |
| G | G |
| Binary | Binary |

**Example (Join Logical File):**
```dds
     A          R RECORD1                   JFILE(PF1 PF2)
     A          J                           JOIN(1 2)
     A                                      JFELD(CITY CITY)
     A            ADDRESS                   JREF(2)
     A            CITY               I      SST(ADDRESS 21 10)
     A                                      JREF(2)
     A            SYEAR              I      SST(SALESDATE 5)
```

**Example (Simple Logical File):**
```dds
     A          R REC1                      PFILE(PFA)
     A            LASTNAME           I      SST(NAME 10 10)
     A          K LASTNAME
```

---

## TRNTBL (Translation Table)

**Applies to:** Field level

**Description:** Specifies a translation table for converting data between physical and program.

**Syntax:**
```dds
TRNTBL([library-name/]translation-table-name)
```

**Rules:**
- Field must be character type
- Field length cannot be redefined in logical file
- Cannot be used with date, time, or timestamp fields
- ICU tables not allowed
- Up to 99 different translation tables per logical file
- Translation occurs on read from physical file

**Example:**
```dds
00010A          R RECORD1                   PFILE(PF1)
00020A            CHAR1              I      TRNTBL(LIB1/TBL1)
00030A            CHAR2           A  I      TRNTBL(LIB2/TBL2)
```

---

## UNIQUE (Unique)

**Applies to:** File level

**Description:** Specifies that records with duplicate key values are not allowed.

**Syntax:**
```dds
UNIQUE[(*INCNULL | *EXCNULL)]
```

**Parameters:**
- `*INCNULL` (default): Include null values in duplicate checking
- `*EXCNULL`: Exclude null values from duplicate checking

**Requirements:**
- Must specify MAINT(*IMMED) on CRTLF command
- Access path maintained immediately

**Restrictions:** Cannot be used with FIFO, LIFO, FCFO, or REFACCPTH

**Example:**
```dds
00040A                                      UNIQUE
00050A          R CUSREC                    PFILE(CUSMSTP)
00060A                                      TEXT('Logical File Master Record')
00070A            CUST
00080A            NAME
00090A            ADDR
```

---

## Logical File Types

### Simple Logical File
- One record format
- One physical file on PFILE
- Optional key fields, select/omit fields

### Multiple-Format Logical File
- Multiple record formats
- Multiple physical files possible
- Each record format uses fields common to all PFILE files

### Join Logical File
- One record format
- Up to 256 physical files on JFILE
- Join fields from multiple files

---

## Access Path Types for Logical Files

### Arrival Sequence Access Path
- No key fields specified
- Cannot specify select/omit unless DYNSLT keyword used
- Only one record format with one PFILE allowed

### Keyed Sequence Access Path
- At least one key field per record format (for multi-format files)
- Select/omit fields allowed

### Reference Access Path (REFACCPTH)
- Copies access path information from another file
- Cannot contain key, select, or omit fields
- Not allowed in join logical files

---

## Select/Omit Field Rules

Select/omit fields determine which records are returned when program reads the logical file.

**Syntax:**
- Position 17: `S` = Select, `O` = Omit
- Position 19-28: Field name
- Keywords: COMP, RANGE, VALUES

**Processing:**
- Records tested against specifications in order
- ALL keyword can define final action

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

## Record Format Specification Methods

1. **Record format name + PFILE/JFILE keyword only**
2. **Record format name + PFILE/JFILE + individual field names**
3. **Record format name + PFILE + FORMAT keyword** (shares field specs from another format)

---

## DDS Code Example - Simple Logical File

```dds
00010A* LOGICAL FILE EXAMPLE
00020A* INVENTORY FORMAT
00030A          R INVFMT                    PFILE(INVENTORY)
00040A          K ITEM
00050A*
00060A* ORDER FORMAT
00070A          R ORDFMT                    PFILE(ORDER)
00080A                                      TEXT('ORDER ANALYSIS')
00090A            ITEM
00100A            ORDER         10
00110A            SUPPLY        +2
00120A            SHPDAT                    CONCAT(SHPMO SHPDA SHPYR)
00130A            QTY            5P         RENAME(QTYDUE)
00140A          K ITEM
00150A          K SHPYR
00160A          K SHPMO
00170A          K SHPDA
00180A          O QTYDUE                    CMP(LT 1)
```

---

## DDS Code Example - Join Logical File

```dds
00010A* Joins fields from two physical files into one record format
00020A          R RECORD1                   JFILE(PF1 PF2)
00030A          J                           JOIN(PF1 PF2)
00040A                                      JFLD(NAME NAME)
00050A            NAME                      JREF(1)
00060A            ADDR
00070A            PHONE
```
