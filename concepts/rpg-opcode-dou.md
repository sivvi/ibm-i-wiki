---
title: DOU (Do Until)
section: 819
---

# DOU (Do Until)

Free-Form Syntax: DOU{(MR)} indicator-expression

## Code

| Factor 1 | Factor 2 | Indicators |
|----------|----------|------------|
| DOU (M/R) | indicator-expression | |

The DOU operation code precedes a group of operations which you want to execute at least once and possibly more than once. Its function is similar to that of the DOUxx operation code. An associated ENDDO statement marks the end of the group. It differs in that the logical condition is expressed by an indicator valued expression (indicator-expression). The operations controlled by the DOU operation are performed until the expression in indicator-expression is true. For information on how operation extenders M and R are used, see "Precision Rules for Numeric Operations" on page 648.

For fixed-format syntax, level and conditioning indicators are valid. Factor 1 must be blank. Extended factor 2 contains the expression to be evaluated.

For more information, see "Compare Operations" on page 607 or "Structured Programming Operations" on page 631.

## Example

```
 *..1....+....2....+....3....+....4....+....5....+....6....+....7...+....
 /FREE
    // In this example, the do loop will be repeated until the F3
    // is pressed.
    dou *inkc;
       do_something();
    enddo;
 
    // The following do loop will be repeated until *In01 is on
    // or until FIELD2 is greater than FIELD3
    dou *in01 or (Field2 > Field3);
       do_something_else ();
    enddo;
 
    // The following loop will be repeated until X is greater than
    // the number of elements in Array
    dou X > %elem (Array);
       Total = Total + Array(x);
       X = X + 1;
    enddo;
 /END-FREE
```

Figure 301. DOU Operation
