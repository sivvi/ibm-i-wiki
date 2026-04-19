---
title: RPG Control Specification Keywords
created: 2026-04-19
updated: 2026-04-19
type: concept
tags: [rpg, rpg-iv, control-specification, h-spec, keywords]
sources: [raw/papers/rpg-reference-7.5.txt]
---

# RPG Control Specification Keywords

Control Specification keywords (H-spec) determine how the program deals with devices, how certain types of information will be represented, and compile options that override defaults on CRTBNDRPG and CRTRPGMOD commands.

## ACTGRP

```
ACTGRP(*STGMDL | *NEW | *CALLER | 'activation-group-name')
```

Specifies the activation group the program is associated with when called.

- `*STGMDL`: Use storage model to determine activation group (QILE or QILETS)
- `*NEW`: Activate into a new activation group
- `*CALLER`: Activate into the caller's activation group
- `'name'`: Use specified activation group name

**Valid only with CRTBNDRPG command.**

## ALLOC

```
ALLOC(*STGMDL | *TERASPACE | *SNGLVL)
```

Specifies the storage model for memory management operations.

- `*STGMDL` (default): Use same storage model as module
- `*SNGLVL`: Single-level storage model
- `*TERASPACE`: Teraspace storage model

## ALTSEQ

```
ALTSEQ{(*NONE | *SRC | *EXT)}
```

Specifies whether an alternate collating sequence is used.

- Not specified or `*NONE`: Normal collating sequence
- `*SRC`: Alternate sequence specified in source
- `*EXT`: Sequence specified by SRTSEQ and LANGID

## ALWNULL

```
ALWNULL(*NO | *INPUTONLY | *USRCTL)
```

Specifies how null-capable fields from externally described database files are handled.

- `*NO`: Cannot process records with null-value fields
- `*INPUTONLY`: Read null-capable records but cannot update/create
- `*USRCTL`: Full control over null-capable fields

## AUT

```
AUT(*LIBRCRTAUT | *ALL | *CHANGE | *USE | *EXCLUDE | 'authorization-list-name')
```

Specifies authority given to users without specific authority to the object.

## BNDDIR

```
BNDDIR('binding-directory-name' {:'binding-directory-name' ...})
```

Specifies binding directories for symbol resolution.

## CCSID Variants

The CCSID keyword can be specified multiple times with different first parameters.

### CCSID(*EXACT)

Controls general handling of CCSIDs within the module. When specified, the compiler makes correct assumptions about CCSIDs of data.

### CCSID(*CHAR{: *JOBRUN | *JOBRUNMIX | *UTF8 | *HEX | number})

Sets default character CCSID for alphanumeric data definitions.

- `*JOBRUN`: Job CCSID at runtime
- `*JOBRUNMIX`: Mixed-byte CCSID related to job CCSID
- `*UTF8`: UTF-8 (CCSID 1208)
- `*HEX`: No CCSID
- `number`: Specific alphanumeric CCSID

### CCSID(*GRAPH{: *JOBRUN | *SRC | *HEX | *IGNORE | number})

Sets default graphic CCSID for DBCS data.

### CCSID(*UCS2{: *UTF16 | number})

Sets default UCS-2 CCSID. `*UTF16` is CCSID 1200.

## CCSIDCVT

```
CCSIDCVT(*EXCP | *LIST)
```

Controls how the compiler handles conversions between data with different CCSIDs.

- `*EXCP`: Throw exception (status code 452) on substitution characters
- `*LIST`: Add conversion summary to listing

## CHARCOUNT

```
CHARCOUNT(*NATURAL | *STDCHARSIZE)
```

Controls how strings are processed.

- `*NATURAL`: Process by natural character size
- `*STDCHARSIZE` (default): Process by bytes/double bytes

## CHARCOUNTTYPES

```
CHARCOUNTTYPES(*UTF8 *UTF16 *JOBRUN *MIXEDEBCDIC *MIXEDASCII)
```

Specifies data types processed by natural character size when CHARCOUNT(*NATURAL) is in effect.

## COPYNEST

```
COPYNEST(number)
```

Maximum depth for /COPY directive nesting (1-2048, default 32).

## COPYRIGHT

```
COPYRIGHT('copyright string')
```

Copyright information up to 256 characters.

## CURSYM

```
CURSYM('sym')
```

Currency symbol for editing (default '$').

## CVTOPT

```
CVTOPT(*{NO}DATETIME *{NO}GRAPHIC *{NO}VARCHAR *{NO}VARGRAPHIC)
```

Controls how externally described database file data types are handled.

- `*DATETIME`: Date/time/timestamp as fixed-length character
- `*GRAPHIC`: DBCS graphic as fixed-length character
- `*VARCHAR`: Variable-length char as fixed-length char
- `*VARGRAPHIC`: Variable-length graphic as fixed-length char

## DATEDIT

```
DATEDIT(fmt{separator})
```

Format for numeric fields using Y edit code. Formats: `*DMY`, `*MDY`, `*YMD`.

## DATEYY

```
DATEYY(*ALLOW | *WARN | *NOALLOW)
```

Controls handling of 2-digit years as 2039 approaches.

- `*ALLOW` (default): No messages
- `*WARN`: Severity-10 warning
- `*NOALLOW`: Severity-30 error

## DATFMT

```
DATFMT(fmt{separator})
```

Internal date format for date literals and default for date fields. Default is `*ISO`.

## DCLOPT

```
DCLOPT(*NOCHGDSLEN)
```

Allows use of %SIZE with data structure parameters when subfields are not fully defined.

## DEBUG

```
DEBUG{(*DUMP | *INPUT | *RETVAL | *XMLSAX | *NO | *YES)}
```

Controls debugging aids generated into module.

- `*DUMP`: Enable DUMP operations
- `*INPUT`: Read all externally described input fields
- `*RETVAL`: View/change procedure return values
- `*XMLSAX`: Generate XML SAX event names array
- `*YES`: Same as `*DUMP:*INPUT`
- `*NO`: No debugging aids

## DECEDIT

```
DECEDIT(*JOBRUN | 'value')
```

Characters used for decimal point and digit separator. Default is '. ' for decimal and ',' for separator.

## DECPREC

```
DECPREC(30 | 31 | 63)
```

Decimal precision for intermediate values in arithmetic expressions.

- `30` (default): Maximum 30 digits
- `31`: Maximum 31 digits
- `63`: Up to 63 digits (maximum)

## DFTACTGRP

```
DFTACTGRP(*YES | *NO)
```

Activation group for the created program.

- `*YES`: Run in default activation group (for OPM compatibility)
- `*NO`: Use activation group from ACTGRP keyword

**Valid only with CRTBNDRPG.**

## DFTNAME

```
DFTNAME(rpg_name)
```

Default program/module name when `*CTLSPEC` is specified on create command.

## ENBPFRCOL

```
ENBPFRCOL(*PEP | *ENTRYEXIT | *FULL)
```

Performance collection settings.

## EXPROPTS

```
EXPROPTS(*MAXDIGITS | *RESDECPOS | *ALWBLANKNUM | *USEDECEDIT | *STRICTKEYS)
```

Expression options.

- `*MAXDIGITS` (default): Standard precision rules
- `*RESDECPOS`: "Result Decimal Position" precision rules
- `*ALWBLANKNUM`: Allow blank for numeric conversion
- `*USEDECEDIT`: Use DECEDIT for numeric conversions
- `*STRICTKEYS`: Strict rules for search arguments

## EXTBININT

```
EXTBININT{(*NO | *YES)}
```

Process externally described binary fields with zero decimal positions as integers.

## FIXNBR

```
FIXNBR(*{NO}ZONED *{NO}INPUTPACKED)
```

Whether invalid decimal data is fixed by compiler.

- `*ZONED`: Fix invalid zoned decimal data
- `*INPUTPACKED`: Set to zero if invalid packed decimal on input

## FLTDIV

```
FLTDIV{(*NO | *YES)}
```

All divide operations computed in floating point.

## FORMSALIGN

```
FORMSALIGN{(*NO | *YES)}
```

First page forms alignment with 1P indicator.

## FTRANS

```
FTRANS{(*NONE | *SRC)}
```

File translation settings.

## GENLVL

```
GENLVL(number)
```

Generation severity level (0-20). Object created only if errors <= specified level.

## INDENT

```
INDENT(*NONE | 'character-value')
```

Structured operation indentation in source listing.

## INTPREC

```
INTPREC(10 | 20)
```

Decimal precision for integer intermediate values in binary operations.

## LANGID

```
LANGID(*JOBRUN | *JOB | 'language-identifier')
```

Language identifier for sort sequence tables.

## MAIN

```
MAIN(main_procedure_name)
```

Indicates a linear-main module with specified main procedure.

## NOMAIN

Indicates no main procedure in module. Cannot use CRTBNDRPG; use CRTSRVPGM or CRTPGM with another module.

## OPENOPT

```
OPENOPT(*{NO}INZOFL **{NO}CVTDATA)
```

File opening options.

- `*INZOFL`: Reset overflow indicator when file opened
- `*CVTDATA`: Default DATA(*CVT) for database files

## OPTIMIZE

```
OPTIMIZE(*NONE | *BASIC | *FULL)
```

Optimization level.

- `*NONE`: No optimization (debuggable)
- `*BASIC`: Some optimization
- `*FULL`: Most efficient code

## OPTION

```
OPTION(*{NO}XREF *{NO}GEN *{NO}SECLVL *{NO}SHOWCPY *{NO}EXPDDS *{NO}EXT *{NO}SHOWSKP *{NO}SRCSTMT *{NO}DEBUGIO *{NO}UNREF)
```

Compile options for source member compilation.

## PGMINFO

```
PGMINFO(*PCML | *NO | *DCLCASE {: *MODULE {: *V6 | *V7 | *V8}})
```

Program-interface information generation (PCML format).

## PRFDTA

```
PRFDTA(*NOCOL | *COL)
```

Profiling data collection. `*COL` requires OPTIMIZE(*FULL).

## REQPREXP

```
REQPREXP(*NO | *WARN | *REQUIRE)
```

Whether prototypes are required for main procedure and exported procedures.

## SRTSEQ

```
SRTSEQ(*HEX | *JOB | *JOBRUN | *LANGIDUNQ | *LANGIDSHR | 'sort-table-name')
```

Sort sequence table for the program.

## STGMDL

```
STGMDL(*INHERIT | *SNGLVL | *TERASPACE)
```

Storage model for program or module.

## TEXT

```
TEXT(*SRCMBRTXT | *BLANK | 'description')
```

Object description (max 50 characters).

## THREAD

```
THREAD(*CONCURRENT | *SERIALIZE)
```

Thread safety settings for module.

- `*CONCURRENT`: Multiple threads can run simultaneously (thread-local storage)
- `*SERIALIZE`: Only one thread at a time

## TIMFMT

```
TIMFMT(fmt{separator})
```

Internal time format. Default is `*ISO`.

## TRUNCNBR

```
TRUNCNBR(*YES | *NO)
```

Numeric overflow handling.

- `*YES`: Truncate and continue
- `*NO`: Generate error on overflow

## USRPRF

```
USRPRF(*USER | *OWNER)
```

User profile for running program.

**Valid only with CRTBNDRPG.**

## VALIDATE

```
VALIDATE(*NODATETIME)
```

Skip validation for Date, Time, Timestamp data. Can improve performance but may propagate invalid data.

## Related Pages

- [[rpg-iv-basics]] — RPG IV basics and program structure
- [[rpg-definition-specs]] — Definition Specification keywords
- [[ile-concepts]] — ILE concepts and module binding
- [[rpg-free-format]] — Free-format RPG syntax
