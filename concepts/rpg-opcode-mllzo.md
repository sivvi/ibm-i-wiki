---
title: MLLZO (Move Low to Low Zone)
section: 861
---

# MLLZO (Move Low to Low Zone)

## Free-Form Syntax

```
(not allowed - use the %BITAND and %BITOR built-in functions. See Figure 197 on
page 661.)
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| MLLZO | Source field | Target field | |

## Description

The MLLZO operation moves the zone portion of a character from the rightmost zone in factor 2 to the rightmost zone in the result field. Factor 2 and the result field can be either character data or numeric data. For further information on the MLLZO, see "Move Zone Operations" on page 627.

The function of the MLLZO operation is shown in Figure 180 on page 628.
