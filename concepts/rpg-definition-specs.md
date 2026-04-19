---
title: RPG Definition Specification Keywords
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, definition-specification, d-spec, keywords]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG Definition Specification Keywords

Definition Specification keywords (D-spec) define variables, data structures, subprocedure interfaces, and named constants.

## Summary Tables

### Data Structure, Standalone Fields, and Named Constants Keywords

| Keyword | Data Structure | DS Subfield | External Subfield | Standalone Field | Named Constant |
|---------|---------------|-------------|------------------|------------------|----------------|
| ALIGN | A | - | - | A | - |
| ALT | A | A | A | A | - |
| ALTSEQ | A | A | A | A | - |
| ASCEND | A | A | A | A | - |
| BASED | A | A | - | - | - |
| CCSID | A | A | A | A | - |
| CONST | R | - | - | - | R |
| CTDATA | A | A | A | A | - |
| DATE | A | A | A | A | - |
| DATFMT | A | A | A | A | - |
| DIM | A | A | A | A | - |
| DTAARA | A | A | A | A | - |
| EXPORT | A | A | - | - | - |
| EXT | A | - | - | - | - |
| EXTFLD | - | - | A | - | - |
| EXTFMT | A | A | A | A | - |
| EXTNAME | A | - | - | - | - |
| FLOAT | A | A | A | A | - |
| FROMFILE | A | A | A | A | - |
| IMPORT | A | A | - | - | - |
| IND | A | A | A | A | - |
| INT | A | A | A | A | - |
| INZ | A | A | A | A | - |
| LEN | A | A | A | A | - |
| LIKE | A | A | A | - | - |
| LIKEDS | A | A | - | - | - |
| LIKEREC | A | A | - | - | - |
| NOOPT | A | A | - | - | - |
| NULLIND | A | A | A | - | - |
| OBJECT | A | A | A | A | - |
| OCCURS | A | A | - | - | - |
| OVERLAY | A | A | - | - | - |
| PACKED | A | A | A | A | - |
| PACKEVEN | A | - | - | A | - |
| PERRCD | A | A | A | A | - |
| POINTER | A | A | - | - | - |
| POS | A | A | - | - | - |
| PREFIX | A | A | A | - | - |
| PROCPTR | A | A | - | - | - |
| PSDS | A | - | - | - | - |
| QUALIFIED | A | - | - | - | - |
| STATIC | A | A | - | A | - |
| TEMPLATE | A | A | - | A | - |
| TIME | A | A | A | A | - |
| TIMESTAMP | A | A | A | A | - |
| TIMFMT | A | A | A | A | - |
| TOFILE | A | A | A | A | - |
| UCS2 | A | A | A | A | - |
| UNS | A | A | A | A | - |
| VARCHAR | A | A | A | A | - |
| VARGRAPH | A | A | A | A | - |
| VARUCS2 | A | A | A | A | - |
| VARYING | A | A | A | A | - |
| ZONED | A | A | A | A | - |

## ALIGN

Aligns subfields in a data structure on their natural boundaries.

```
DCL-DS myDS QUALIFIED ALIGN;
```

## ALT

Alternate name for a field (used with renamed fields).

## ALTSEQ

Specifies an alternate collating sequence for a character field.

## ASCEND

Specifies ascending sequence for an array (for indexing).

## BASED

```
BASED(ptrvar)
```

Defines a based data structure or field. Storage is allocated dynamically via pointer.

## CCSID

```
CCSID(*EXACT | *NOEXACT | *CHAR:value | *UCS2:value | *GRAPH:value)
```

Specifies the CCSID for character, UCS-2, or graphic data.

## CLASS

For Java object parameters, specifies the Java class name.

## CONST

For named constants, specifies the constant value. For parameters, indicates passed by constant reference.

```
DCL-C MAX_RECORDS CONST(1000);
DCL-PR proc PROCEDURE CONST;
```

## CTDATA

```
CTDATA(type)
```

Compile-time array data. `type` can be `*CHAR`, `*UCS2`, or `*GRAPH`.

## DATE

```
DATE{(format)}
```

Free-form date field declaration.

## DATFMT

```
DATFMT(format{separator})
```

Date format for a field.

## DTAARA

```
DTAARA(*DEFAULT | *INPUT | *VAR | 'name')
```

Data area association.

## DIM

```
DIM(expression)
```

Array dimension (number of elements).

## EDCVLEN

Length of a user-encoded field in an externally-described file.

## ENDJA / ENDJO

End of justified data area (ENDJA) or end of justified output area (ENDJO).

## EXPORT

Export a variable for use by other modules.

## EXT

External data structure (no explicit subfields).

## EXTFLD

External field name mapping.

## EXTFMT

External format for a field.

## EXTNAME

```
EXTNAME(filename{:recordformat})
```

Externally described file name.

## FLOAT

```
FLOAT(4 | 8)
```

Floating-point numeric field (4 bytes = single, 8 bytes = double).

## FORMAT

Format name for print file field.

## FROMFILE

Source file for compile-time array or table.

## IMPORT

Import a variable from another module.

## IND

Indicator data type (1-byte character).

## INT

```
INT(digits)
```

Integer numeric field (3, 5, 10, or 20 digits).

## INZ

```
INZ(*BLANKS | *ZEROS | *NULL | value)
```

Initialization value.

## LEN

Length for free-form definitions.

## LIKE

```
LIKE(fieldname)
```

Define field like another field.

## LIKEDS

```
LIKEDS(data_structure_name)
```

Define data structure like another data structure.

## LIKEREC

```
LIKEREC(filename.recordformat{:*INPUT|*OUTPUT})
```

Define data structure like an externally described record format.

## NOOPT

Prevent optimization of a field.

## NULLIND

```
NULLIND(fieldname)
```

Null indicator for a null-capable field.

## OBJECT

Java object type specification.

## OCCURS

Number of occurrences for an array in a data structure.

## ODP

Indicates an ODP (Open Data Path) is required.

## OPCODE

For special fields, specifies the operation code.

## OPTION

For prototyped parameters.

## OSPCYLEN

```
OSPCYLEN(start:number)
```

Overlay several fields starting at a position.

## OVERLAY

```
OVERLAY(name{:offset})
```

Overlay part of a data structure.

## PACKED

```
PACKED(digits{:decimals})
```

Packed decimal numeric field.

## PACKEVEN

Force packed decimal with even number of digits.

## PERRCD

Records per print page.

## PLIST

Parameter list name for program or procedure call.

## POINTER

Pointer data type. `*PROC` for procedure pointer, `*CLASS` for class method pointer.

## POS

Starting position of a subfield.

## PREFIX

Prefix to add to external field names.

## PRESTRICT

Procedural restriction.

## PROCPTR

Procedure pointer.

## PSDS

Program Status Data Structure.

## QUALIFIED

Subfields accessed with DS.subfield syntax.

## QUALIFIER

Qualifier name for overlay groups.

## RACF

RACF (Resource Access Control Facility) authority.

## READONLY

Parameter passed read-only.

## REQCLR

Required clear operation.

## RTNPARM

```
RTNPARM
```

Return value handled as hidden parameter.

## RTVCODET

Retrieve code text.

## RTVSYSVAL

Retrieve system value.

## SAA

SAA (Systems Application Architecture) compliance.

## SPCPAD

Space padding for character field.

## STATIC

```
STATIC{(*ALLTHREAD)}
```

Static storage for local variable. `*ALLTHREAD` shares across threads.

## STGMDL

Storage model for storage management operations.

## SUFFIX

Suffix to add to external field names.

## TEMPLATE

Template definition for use only with LIKE/LIKEDS.

## TIMFMT

```
TIMFMT(format{separator})
```

Time format.

## UNS

```
UNS(digits)
```

Unsigned integer (3, 5, 10, or 20 digits).

## USTRUCT

Overlay structure.

## USROPT

User options.

## VARLEN

Variable length field.

## VARYING

```
VARYING{(2 | 4)}
```

Variable-length character, graphic, or UCS-2 (2 or 4 byte length prefix).

## VARCHAR

```
VARCHAR(length{:2|4})
```

Variable-length character field.

## VARGRAPH

```
VARGRAPH(length{:2|4})
```

Variable-length graphic field.

## VARUCS2

```
VARUCS2(length{:2|4})
```

Variable-length UCS-2 field.

## ZONED

```
ZONED(digits{:decimal-positions})
```

Zoned decimal numeric field.

## Free-Form Data Type Keywords

These keywords are used in free-form definitions to specify data types:

| Keyword | Description |
|---------|-------------|
| CHAR(length) | Fixed-length character |
| VARCHAR(length{:2\|4}) | Variable-length character |
| GRAPH(length) | Fixed-length graphic (DBCS) |
| VARGRAPH(length{:2\|4}) | Variable-length graphic |
| UCS2(length) | Fixed-length UCS-2 |
| VARUCS2(length{:2\|4}) | Variable-length UCS-2 |
| DATE{(format)} | Date |
| TIME{(format)} | Time |
| TIMESTAMP{(fractional-seconds)} | Timestamp |
| INT(digits) | Integer (3, 5, 10, 20) |
| UNS(digits) | Unsigned (3, 5, 10, 20) |
| FLOAT(4\|8) | Floating point |
| PACKED(digits{:dec}) | Packed decimal |
| ZONED(digits{:dec}) | Zoned decimal |
| IND | Indicator |
| POINTER | Pointer |
| PROCPTR | Procedure pointer |

## Related Pages

- [[rpg-iv-basics]] — RPG IV basics and program structure
- [[rpg-control-specs]] — Control Specification keywords
- [[rpg-iv-data-types]] — Data types in RPG IV
- [[rpg-free-format]] — Free-format RPG syntax
- [[ile-concepts]] — ILE concepts and module binding
