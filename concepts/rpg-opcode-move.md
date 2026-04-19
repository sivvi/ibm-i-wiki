---
title: MOVE (Move)
section: 867
---

# MOVE (Move)

## Free-Form Syntax

```
(not allowed - use the EVAL or EVALR operations, or built-in functions
such as %CHAR, %DATE, %DEC , %DECH, %GRAPH, %INT, %INTH, %TIME,
%TIMESTAMP , %UCS2, %UNS, or %UNSH )
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| MOVE (P) | Data Attributes | Source field | Target field |
| | | | + |
| | | | - |
| | | | ZB |

## Description

The MOVE operation transfers characters from factor 2 to the result field. Moving starts with the rightmost character of factor 2.

When moving Date, Time or Timestamp data, factor 1 must be blank unless either the source or the target is a character or numeric field. Otherwise, factor 1 contains the date or time format compatible with the character or numeric field that is the source or target of the operation. For information on the formats that can be used see "Date Data Type" on page 307, "Time Data Type" on page 310, and "Timestamp Data Type" on page 312.

If the source or target is a character field, you may optionally indicate the separator following the format in factor 1. Only separators that are valid for that format are allowed.

If factor 2 is *DATE or UDATE and the result is a Date field, factor 1 is not required. If factor 1 contains a date format it must be compatible with the format of *DATE or UDATE as specified by the DATEDIT keyword on the control specification.

When moving character, graphic, UCS-2, or numeric data, if factor 2 is longer than the result field, the excess leftmost characters or digits of factor 2 are not moved. If the result field is longer than factor 2, the excess leftmost characters or digits in the result field are unchanged, unless padding is specified.

You cannot specify resulting indicators if the result field is an array; you can specify them if it is an array element, or a non-array field.

If factor 2 is shorter than the length of the result field, a P specified in the operation extender position causes the result field to be padded on the left after the move occurs.

Float numeric fields and literals are not allowed as Factor 2 or Result-Field entries.

If CCSID(*GRAPH : IGNORE) is specified or assumed for the module, MOVE operations between UCS-2 and graphic data are not allowed.

When moving variable-length character, graphic, or UCS-2 data, the variable-length field works in exactly the same way as a fixed-length field with the same current length. A MOVE operation does not change the length of a variable-length result field.

For further information on the MOVE operation, see "Move Operations" on page 622 or "Conversion Operations" on page 608.
