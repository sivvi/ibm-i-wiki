---
title: LEAVESR (Leave a Subroutine)
section: 857
---

# LEAVESR (Leave a Subroutine)

## Free-Form Syntax

```
LEAVESR
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| LEAVESR | | | |

## Description

The LEAVESR operation exits a subroutine from any point within the subroutine. Control passes to the ENDSR operation for the subroutine. LEAVESR is allowed only from within a subroutine.

The control level entry (positions 7 and 8) can be SR or blank. Conditioning indicator entries (positions 9 to 11) can be specified.

For more information, see "Subroutine Operations" on page 634.
