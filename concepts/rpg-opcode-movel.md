---
title: MOVEL (Move Left)
section: 888
---

# MOVEL (Move Left)

## Free-Form Syntax

```
not allowed - use EVAL, or built-in functions such as %CHAR, %DATE, %DEC ,
%DECH, %GRAPH, %INT, %INTH, %TIME, %TIMESTAMP , %UCS2, %UNS, or
%UNSH
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| MOVEL (P) | Data Attributes | Source field | Target field |
| | | | + |
| | | | - |
| | | | ZB |

## Description

The MOVEL operation transfers characters from factor 2 to the result field. Moving begins with the leftmost character in factor 2. You cannot specify resulting indicators if the result field is an array. You can specify them if the result field is an array element, or a non-array field.

When data is moved to a numeric field, the sign (+ or -) of the result field is retained except when factor 2 is as long as or longer than the result field. In this case, the sign of factor 2 is used as the sign of the result field.

Factor 1 can contain a date or time format to specify the format of a character or numeric field that is the source or target of the operation. For information on the formats that can be used see "Date Data Type" on page 307, "Time Data Type" on page 310, and "Timestamp Data Type" on page 312.

If the source or target is a character field, you may optionally indicate the separator following the format in factor 1. Only separators that are valid for that format are allowed.

If factor 2 is *DATE or UDATE and the result is a Date field, factor 1 is not required. If factor 1 contains a date format it must be compatible with the format of *DATE or UDATE in factor 2 as specified by the DATEDIT keyword on the control specification.

If factor 2 is longer than the result field, the excess rightmost characters of factor 2 are not moved. If the result field is longer than factor 2, the excess rightmost characters in the result field are unchanged, unless padding is specified.

Float numeric fields and literals are not allowed as Factor 2 or Result-Field entries.

If factor 2 is UCS-2 and the result field is character, or if factor 2 is character and the result field is UCS-2, the number of characters moved is variable since the character data may or may not contain shift characters and graphic characters. For example, five UCS-2 characters can convert to:
- Five single-byte characters
- Five double-byte characters
- A combination of single-byte and double-byte characters with shift characters separating the modes

If the resulting data is too long to fit the result field, the data will be truncated. If the result is single-byte character, it is the responsibility of the user to ensure that the result contains complete characters, and contains matched SO/SI pairs.

### Summary of MOVEL Operation Rules

A summary of the rules for MOVEL operation for four conditions based on field lengths:

**1. Factor 2 is the same length as the result field:**
- If factor 2 and the result field are numeric, the sign is moved into the rightmost position.
- If factor 2 is numeric and the result field is character, the sign is moved into the rightmost position.
- If factor 2 is character and the result field is numeric, a minus zone is moved into the rightmost position if the zone from the rightmost position of factor 2 is a hexadecimal D (minus zone).
- If factor 2 and the result field are character, all characters are moved.
- If factor 2 and the result field are both graphic or UCS-2, all graphic or UCS-2 characters are moved.

**2. Factor 2 is longer than the result field:**
- If factor 2 and the result field are numeric, the sign from the rightmost position of factor 2 is moved into the rightmost position of the result field.
- If factor 2 is numeric and the result field is character, the result field contains only numeric characters.
- If factor 2 is character and the result field is numeric, a minus zone is moved into the rightmost position if the zone from the rightmost position of factor 2 is a hexadecimal D.
- If factor 2 and the result field are character, only the number of characters needed to fill the result field are moved.

**3. Factor 2 is shorter than the result field:**
- If factor 2 is either numeric or character and the result field is numeric, the digit portion of factor 2 replaces the contents of the leftmost positions of the result field. The sign in the rightmost position of the result field is not changed.
- If factor 2 is either numeric or character and the result field is character data, the characters in factor 2 replace the equivalent number of leftmost positions in the result field.

**4. Factor 2 is shorter than the result field and P is specified:**
- The move is performed as described above.
- The result field is padded from the right.

When moving variable-length character, graphic, or UCS-2 data, the variable-length field works in exactly the same way as a fixed-length field with the same current length. A MOVEL operation does not change the length of a variable-length result field.

For further information on the MOVEL operation, see "Move Operations" on page 622, "Date Operations" on page 612, or "Conversion Operations" on page 608.
