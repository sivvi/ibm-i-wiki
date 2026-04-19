---
title: WHEN-IS (When the SELECT Operand is Equal to the WHEN-IS Operand)
section: Operations, Expressions, and Functions
---

# WHEN-IS (When the SELECT Operand is Equal to the WHEN-IS Operand)

Free-Form Syntax: WHEN-IS expression

## Code

| Factor 1 | Extended Factor 2 |
|----------|------------------|
| WHEN-IS | expression |

## Description

The WHEN-IS operation code is similar to the WHEN operation code in that it controls the processing of lines in a SELECT operation. It differs in that the statements following the WHEN-IS statement are performed when the expression specified as the operand of the SELECT that begins the SELECT group is equal to the expression specified as the operand of the WHEN-IS statement.

See WHEN-IN for another operation code that can be used when the SELECT statement has an operand.

For more information, see "Compare Operations" on page 607 or "Structured Programming Operations" on page 631.

### Examples of the WHEN-IS operation

In the following example:
1. Variable employeeType is specified as the first operand of the SELECT statement.
2. If the value of employeeType is equal to 'MANAGER', the statements following the first WHEN-IS statement are executed, and then control passes to the ENDSL statement.
3. If the comparison for the first WHEN-IS statement was not equal, and the value of employeeType is equal to 'OWNER', the statements following the second WHEN-IS statement are executed, and then control passes to the ENDSL statement.
4. If the comparison for the first two WHEN-IS statements were not equal, and the value of employeeType is in the list specified by the %LIST built-in function, the statements following the WHEN-IN statement are executed, and then control passes to the ENDSL statement.
5. If none of the comparisons for the WHEN-IS or WHEN-IN statemnents are satisfied, then control passes to statements following the OTHER statement.

 DCL-S employeeType CHAR(10);
 SELECT employeeType; //  1 
 WHEN-IS 'MANAGER'; //  2 
    ...
 WHEN-IS 'OWNER'; //  3 
    ...
 WHEN-IN %LIST('REGULAR' : 'NEW'); //  4 
    ...
 OTHER; //  5 
    ...
 ENDSL;

For more examples of the WHEN-IS operation, see "SELECT (Begin a Select Group)" on page 933.
