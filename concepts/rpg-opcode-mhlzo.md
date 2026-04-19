---
title: MHLZO (Move High to Low Zone)
section: 861
---

# MHLZO (Move High to Low Zone)

## Free-Form Syntax

```
(not allowed - use the %BITAND and %BITOR built-in functions. See Figure 197 on
page 661.)
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| MHLZO | Source field | Target field | |

## Description

The MHLZO operation moves the zone portion of a character from the leftmost zone in factor 2 to the rightmost zone in the result field. Factor 2 must be defined as a character field. The result field can be character or numeric data. For further information on the MHLZO operation, see "Move Zone Operations" on page 627.

The function of the MHLZO operation is shown in Figure 180 on page 628.
