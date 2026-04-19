---
title: Z-SUB (Zero and Subtract)
section: Operations, Expressions, and Functions
---

# Z-SUB (Zero and Subtract)

Free-Form Syntax: (not allowed - use the EVAL operation code)

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| Z-SUB (H) | | | |
| | Subtrahend | Difference | + - Z |

## Description

Factor 2 is subtracted from a field of zeros. The difference, which is the negative of factor 2, is placed in the result field. You can use the operation to change the sign of a field. Factor 1 is not used. Factor 2 must be numeric and can contain one of the following: an array, array element, field, figurative constant, literal, named constant, subfield, or table name.

The result field must be numeric, and can contain one of the following: an array, array element, field, subfield, or table name.

Half-adjust can be specified.

For the rules for the Z-SUB operation, see "Arithmetic Operations" on page 597.

See Figure 172 on page 600 for an example of the Z-SUB operation.
