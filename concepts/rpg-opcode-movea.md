---
title: MOVEA (Move Array)
section: 881
---

# MOVEA (Move Array)

## Free-Form Syntax

```
(not allowed — use %SUBARR or one or more String Operations)
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| MOVEA (P) | Source | Target | |
| | | | + |
| | | | - |
| | | | ZB |

## Description

The MOVEA operation transfers character, graphic, UCS-2, or numeric values from factor 2 to the result field. (Certain restrictions apply when moving numeric values.) Factor 2 or the result field must contain an array. Factor 2 and the result field cannot specify the same array even if the array is indexed. You can:

- Move several contiguous array elements to a single field
- Move a single field to several contiguous array elements
- Move contiguous array elements to contiguous elements of another array.

Movement of data starts with the first element of an array if the array is not indexed or with the element specified if the array is indexed. The movement of data ends when the last array element is moved or filled. When the result field contains the indicator array, all indicators affected by the MOVEA operation are noted in the cross-reference listing.

For more information, see "Array Operations" on page 601, "Move Operations" on page 622, or "Date Operations" on page 612.

### Character, graphic, and UCS-2 MOVEA Operations

Both factor 2 and the result field must be the same type - either character, graphic, or UCS-2. Graphic or UCS-2 CCSIDs must be the same, unless one of the CCSIDs is 65535, or in the case of graphic fields, CCSID(*GRAPH: *IGNORE) was specified on the control specification.

On a character, graphic, or UCS-2 MOVEA operation, movement of data ends when the number of characters moved equals the shorter length of the fields specified by factor 2 and the result field; therefore, the MOVEA operation could end in the middle of an array element. Variable-length arrays are not allowed.

### Numeric MOVEA Operations

Moves are only valid between fields and array elements with the same numeric length defined. Factor 2 and the result field entries can specify numeric fields, numeric array elements, or numeric arrays; at least one must be an array or array element. The numeric types can be binary, packed decimal, or zoned decimal but need not be the same between factor 2 and the result field.

Factor 2 can contain a numeric literal if the result field entry specifies a numeric array or numeric array-element:
- The numeric literal cannot contain a decimal point.
- The length of the numeric literal cannot be greater than the element length of the array or array element specified in the result field.

Decimal positions are ignored during the move and need not correspond. Numeric values are not converted to account for the differences in the defined number of decimal places.

The figurative constants *BLANK, *ALL, *ON and *OFF are not valid in factor 2 of a MOVEA operation on a numeric array.

### General MOVEA Operations

If you need to use a MOVEA operation in your application, but restrictions on numeric MOVEA operations prevent you, you might be able to use character MOVEA operations. If the numeric array is in zoned decimal format:
- Define the numeric array as a subfield of a data structure
- Redefine the numeric array in the data structure as a character array.

If a figurative constant is specified with MOVEA, the length of the constant generated is equal to the portion of the array specified. For figurative constants in numeric arrays, the element boundaries are ignored except for the sign that is put in each array element. Examples are:
- `MOVEA *BLANK ARR(X)` - Beginning with element X, the remainder of ARR will contain blanks.
- `MOVEA *ALL'XYZ' ARR(X)` - ARR has 4-byte character elements. Beginning with element X, the remainder of the array will contain 'XYZXYZXYZXYZ...'.

For character, graphic, UCS-2, and numeric MOVEA operations, you can specify a P operation extender to pad the result from the right.

For further information on the MOVEA operation, see "Move Operations" on page 622.
