---
title: "RPG IV Built-In Functions (BIFs)"
description: "Complete reference for all 95 RPG IV Built-In Functions"
keywords: [RPG, RPGIV, BIF, Built-In Functions, IBM i, AS/400]
---

# RPG IV Built-In Functions (BIFs)

This reference documents all 95 Built-In Functions (BIFs) available in RPG IV.

## %ABS (Absolute Value of Expression)

**Syntax:**
```
%ABS(numeric expression)
```

%ABS returns the absolute value of the numeric expression specified as the parameter. If the value of the numeric expression is non-negative, the value is returned unchanged. If the value is negative, the value returned is the value of the expression but with the negative sign removed. %ABS may be used either in expressions or as parameters to keywords. When used with keywords, the operand must be a numeric literal, a constant name representing a numeric value, or a built-in function with a numeric value known at compile-time.

**Examples:**
```rpgle
D f8         s              8f   inz (-1)
D i10        s             10i 0 inz (-123)
D p7         s              7p 3 inz (-1234.567)
f8  = %abs (f8);        // "f8" is now 1.
i10 = %abs (i10 - 321); // "i10" is now 444.
p7  = %abs (p7);        // "p7" is now 1234.567.
```

*For more information, see “Arithmetic Operations” on page 597 or “Built-in Functions” on page 590.*

## %ADDR (Get Address of Variable)

**Syntax:**
```
%ADDR(variable)
```
```
%ADDR(varying-length variable : *DATA)
```

%ADDR returns a value of type basing pointer. The value is the address of the specified variable. It may only be compared with and assigned to items of type basing pointer. %ADDR returns the address of the data portion of a variable-length field when *DATA is specified as the second parameter of %ADDR. If %ADDR with an array index parameter is specified as parameter for definition specification keywords INZ or CONST, the array index must be known at compile-time. The index must be either a numeric literal or a numeric constant. In an EVAL operation where the result of the assignment is an array with no index, %ADDR on the right hand side of the assignment operator has a different meaning depending on the argument for the %ADDR. If the argument for %ADDR is an array name without an index and the result is an array name, each element of the result array will contain the address of the beginning of the argument array. If the argument for %ADDR is an array name with an index of (*), then each element of the result array will contain the address of the corresponding element in the argument array. This is illustrated in Figure 191 on page 656. If the variable specified as parameter is a table, multiple occurrence data structure, or subfield of a multiple occurrence data structure, the address will be the address of the current table index or occurrence number. If the variable is based, %ADDR returns the value of the basing pointer for the variable. If the variable is a subfield of a based data structure, the value of %ADDR is the value of the basing pointer plus the offset of the subfield. If the variable is specified as a PARM of the *ENTRY PLIST, %ADDR returns the address passed to the program by the caller. When the argument of %ADDR cannot be modified, %ADDR can only be used in a comparison operation. An example of an argument that cannot be modified is a read-only reference parameter (CONST keyword

## %ALLOC (Allocate Storage)

**Syntax:**
```
%ALLOC(num)
```

%ALLOC returns a pointer to newly allocated heap storage of the length specified. The newly allocated storage is uninitialized. The parameter must be a non-float numeric value with zero decimal places. The length specified must be between 1 and the maximum size allowed. The maximum size allowed depends on the type of heap storage used for RPG memory management operations due to the ALLOC keyword on the Control specification. If the module uses teraspace heap storage, the maximum size allowed is 4294967295 bytes. Otherwise, the maximum size allowed is 16776704 bytes. The maximum size available at runtime may be less than the maximum size allowed by RPG. If the operation cannot complete successfully, exception 00425 or 00426 is issued.

**Examples:**
```rpgle
// Allocate an area of 200 bytes
pointer = %ALLOC(200);
```

*For more information, see “Memory Management Operations” on page 621.*

## %BITAND (Bitwise AND Operation)

**Syntax:**
```
%BITAND(expr:expr{:expr...})
```

%BITAND returns the bit-wise ANDing of the bits of all the arguments. That is, the result bit is ON when all

## %BITNOT (Invert Bits)

**Syntax:**
```
%BITNOT(expr)
```

%BITNOT returns the bit-wise inverse of the bits of the argument. That is, the result bit is ON when the corresponding bit in the argument is OFF, and OFF otherwise. The argument to this built-in function can be either character or numeric. For numeric arguments, if they are not integer or unsigned, they are first converted to integer. If the value does not fit in an 8-byte integer, a numeric overflow exception is issued. %BITNOT takes just one argument. The result type is the same as the types of the arguments. For numeric arguments, the result is unsigned if all arguments are unsigned, and integer otherwise. The length is the length of the largest operand. If the arguments have different lengths, they are padded on the left with bit zeros for numeric arguments. %BITNOT can be coded in any expression. It can also be coded as the argument to a File or Definition Specification keyword if all arguments are known at compile-time. If all arguments of this built-in function are hex literals, the compiler produces a constant-folded result that is a hex literal. Please see Figure 194 on page 660 for an example demonstrating the use of %BITNOT.

## %BITOR (Bitwise OR Operation)

**Syntax:**
```
%BITOR(expr:expr{:expr...})
```

%BITOR returns the bit-wise ORing of the bits of all the arguments. That is, the result bit is ON when any of the corresponding bits in the arguments are ON, and OFF otherwise. The arguments to this built-in function can be either character or numeric. For numeric arguments, if they are not integer or unsigned, they are first converted to integer. If the value does not fit in an 8-byte integer, a numeric overflow exception is issued. %BITOR can have two or more arguments. All arguments must be the same type, either character or numeric. However, when coded as keyword parameters, these two BIFs can have only two arguments. The result type is the same as the types of the arguments. For numeric arguments, the result is unsigned

## %BITXOR (Bitwise Exclusive-OR Operation)

**Syntax:**
```
%BITXOR(expr:expr)
```

%BITXOR returns the bit-wise exclusive ORing of the bits of the two arguments. That is, the result bit is ON when just one of the corresponding bits in the arguments are ON, and OFF otherwise. The argument to this built-in function can be either character or numeric. For numeric arguments, if they are not integer or unsigned, they are first converted to integer. If the value does not fit in an 8-byte integer, a numeric overflow exception is issued. %BITXOR takes exactly two arguments. The result type is the same as the types of the arguments. For numeric arguments, the result is unsigned if all arguments are unsigned, and integer otherwise. The length is the length of the largest operand. If the arguments have different lengths, they are padded on the left with bit zeros for numeric arguments. Shorter character arguments are padded on the right with bit zeros . %BITXOR can be coded in any expression. It can also be coded as the argument to a File or Definition Specification keyword if all arguments are known at compile-time. If all arguments of this built-in function are hex literals, the compiler produces a constant-folded result that is a hex literal.

## %CHAR (Convert to Character Data)

**Syntax:**
```
%CHAR(expression{: format | ccsid})
```

%CHAR converts the value of the expression from character, graphic, UCS-2, numeric, date, time or

## %CHARCOUNT (Return the Number of Characters)

**Syntax:**
```
%CHARCOUNT(string)
```

%CHARCOUNT returns the number of natural characters in the alphanumeric, graphic, or UCS-2 expression. This may be different from the number of bytes or double bytes that is returned by %LEN if the operand is one of the following: • UTF-16, data type UCS-2 with CCSID(*UTF16) (or CCSID(1200)). • UTF-8, data type CHAR with CCSID(*UTF8) (or CCSID(1208)). • EBCDIC with mixed SBCS and DBCS data. • ASCII with mixed SBCS and DBCS data. See “Processing string data by the natural size of each character” on page 295. Note: The %CHARCOUNT built-in function always returns the number of natural characters, even if the data type and CCSID of the operand is not usually relevant due to the CHARCOUNTTYPES. Example of %CHARCOUNT with a UTF-8 value UTF-8 data can have 1, 2, 3, or 4 bytes per character. Character 'ç' has two bytes. Characters 'a' and 'b' have one byte. 1. The string 'abç' has four bytes. %LEN(string) returns 4. 2. The string 'abç' has three characters. %CHARCOUNT(string) returns 3. DCL-S string VARCHAR(20) CCSID(*UTF8); DCL-S n INT(10); string = 'abç';

**Examples:**
```rpgle
n = %len(string); //  1
// n = 4
n = %charcount(string); //  2
// n = 3
Example of %CHARCOUNT with a mixed SBCS/DBCS EBCDIC value
```

## %CHECK (Check Characters)

**Syntax:**
```
%CHECK(comparator : base {: start { : *NATURAL | *STDCHARSIZE}})
```

%CHECK returns the first position of the string base that contains a character that does not appear in string comparator. If all of the characters in base also appear in comparator, the function returns 0. The check begins at the starting position and continues to the right until a character that is not contained in the comparator string is found. The starting position defaults to 1. The first and second parameters must be of type character, graphic, or UCS-2, fixed or varying length. If the first parameter does not have the same type and CCSID as the second parameter, the first parameter is converted to the type and CCSID of the second parameter. The third parameter, if specified, must be a non-float numeric with zero decimal positions. The third or fourth parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %CHECK operates in CHARCOUNT NATURAL mode. The start position and return value are measured in characters rather than bytes or double bytes. For example, if the base string is a UTF-8 string with the value 'ábç12', and the comparator string is 'çbá', the '1' character is the first character in the base string that does not appear in the comparator string. With CHARCOUNT NATURAL mode, %CHECK returns 4, because the character '1' is the fourth character in the base string. • Specify *STDCHARSIZE to indicate that %CHECK operates in CHARCOUNT STDCHARSIZE mode. In the previous example, with CHARCOUNT STDCHARSIZE mode, %CHECK returns 6 because '1' is the 6th byte in the string. Characters 'á' and 'ç' are 2-byte characters. See “Processing string data by the natural size of each character” on page 295. Note: %CHECK can also operate in CHARCOUNT NATURAL mode due to the /CHARCOUNT compiler directive or the CHARCOUNT Control keyword.

## %CHECKR (Check Reverse)

**Syntax:**
```
%CHECKR(comparator : base {: start {: *NATURAL | *STDCHARSIZE}})
```

%CHECKR returns the last position of the string base that contains a character that does not appear in string comparator. If all of the characters in base also appear in comparator, the function returns 0. The check begins at the starting position and continues to the left until a character that is not contained in the comparator string is found. The starting position defaults to the end of the string. The first and second parameters must be of type character, graphic, or UCS-2, fixed or varying length. If the first parameter does not have the same type and CCSID as the second parameter, the first parameter is converted to the type and CCSID of the second parameter. The third parameter, if specified, must be a non-float numeric with zero decimal positions. The third or fourth parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT

## %CONCAT (Concatenate with Separator)

**Syntax:**
```
%CONCAT(separator : string1 : string2 { : string3 ... { : *NATURAL | *STDCHARSIZE})
```

%CONCAT returns the concatenation of string1, string2, string3, and so on, separated by the separator operand. The operands must have type alphanumeric, UCS-2, or graphic. They cannot be hexadecimal literals or have CCSID(*HEX). If no separator is required, specify *NONE as the separator operand. If a single blank is required as the separator, specify *BLANK or *BLANKS as the separator operand. There must be at least three operands. There is no practical upper limit for the number of operands. The last parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. • Specify *NATURAL to indicate that %CONCAT operates in CHARCOUNT NATURAL mode. • Specify *STDCHARSIZE to indicate that %CONCAT operates in CHARCOUNT STDCHARSIZE mode. For information on how the CHARCOUNT mode affects concatenation, see “Concatenation of data with different character sizes” on page 631. The data type and CCSID of the value returned by the built-in function depends on the data type of the operands. See “Determining the Common Type of Multiple Operands” on page 715. To concatenate the elements of an array, use %CONCATARR.

**Examples:**
```rpgle
1. Concatenate two operands with a separator
DCL-S result VARCHAR(50);
result = %CONCAT(', ' : 'cat' : 'dog');
```

## %CONCATARR (Concatenate Array Elements with Separator)

**Syntax:**
```
%CONCATARR(separator : array { : *NATURAL | *STDCHARSIZE})
```
```
%CONCATARR (Concatenate Array Elements with
```
```
%CONCATARR (Concatenate Array Elements with
```

%CONCATARR returns the concatenation of the elements of the array operand separated by the separator operand. If no separator is required, specify *NONE as the first operand. If a single blank is required as the separator, specify *BLANK or *BLANKS as the first operand. The operands must have type alphanumeric, UCS-2, or graphic. Separator) The second operand must be an array or array expression, including %SUBARR, %LIST, or %SPLIT. It cannot have CCSID(*HEX). The third parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. • Specify *NATURAL to indicate that %CONCATARR operates in CHARCOUNT NATURAL mode. • Specify *STDCHARSIZE to indicate that %CONCATARR operates in CHARCOUNT STDCHARSIZE mode.. For information on how the CHARCOUNT mode affects concatenation, see “Concatenation of data with different character sizes” on page 631. The data type and CCSID of the value returned by the built-in function depends on the data type of the operands. See “Determining the Common Type of Multiple Operands” on page 715. Also see “%CONCAT (Concatenate with Separator)” on page 669.

## %DATA

**Syntax:**
```
%DATA (document {:options})
```
```
%DATA (document {:options}) built-in function
```
```
%DATA('mydoc.txt' : 'doc=file ' + ccsidOpt)
```

%DATA is used as the second operand of the DATA-INTO and DATA-GEN operations. %DATA does not return a value, and it cannot be specified anywhere other than for the DATA-INTO and DATA-GEN operation codes. The first operand of %DATA identifies the document. It can be a constant or variable character or UCS-2 expression. The second operand of %DATA specifies options that control the operation. It can be a constant or variable character expression. See “Specifying the options for %DATA” on page 672. For the DATA-INTO operation • %DATA specifies the document to be parsed, and the options to control how the information from the document is placed in the target RPG variable. • The first operand can be a constant or variable character or UCS-2 expression. containing either an document or the name of a file containing a document. • The second operand of %DATA specifies options that control how the document is to be interpreted, and how the data from the document is to be placed in the RPG variable. See “%DATA options for the DATA-INTO operation code” on page 806 for a complete list of valid options and values. For the DATA-GEN operation • %DATA specifies the location for the document to be placed, and the options to control how the document is generated from the RPG variable in the first operand of DATA-GEN. • The first operand can be a constant or variable character or UCS-2 expression. If the "doc=file" option is specified, it is the name of the file to receive the generated document. If the "doc=file" option is not specified, the generated document is placed in the variable. • The second operand of %DATA specifies options that control how the document is to be generated, and how the data from the RPG variable is to be passed to the generator. See “%DATA options for the DATA-GEN operation code” on page 802 for a complete list of valid options and values. Specifying the options for %DATA The value of the character expression is a list of zero or more options specified in the form optionname1=value1 optionname2=value2 No spaces are allowed between the option name and the equal sign or between the equal sign and the value. However, any number of spaces can appear before, between or following the options. The options can be specified in any case. The following are all valid ways to specify the "doc=file" and "allowextra=yes" options for DATA-INTO: 'doc=file allowextra=yes' '        doc=file     allowextra=yes     ' 'ALLOWEXTRA=YES DOC=FILE     ' 'AllowExtra=Yes Doc=File     ' The following are not valid option strings: Option string The problem with the option string 'doc = file' Spaces around the equal sign are not allowed 'allowextra' Each option must have an equal sign and a value 'badopt=yes' Only valid options are allowed 'allowextra=ok' The 'allowextra' value can only be 'yes' or 'no' When an option is specified more than once, the last value specified is the value that is used. For example, if the "options" operand has the value 'doc=file doc=string' then the parser will use the value "string" for the "doc" option. If the parser discovers an invalid option or invalid value, the operation will fail with status code 00352.

## %DATE (Convert to Date)

%DATE{(expression{:date-format})} %DATE converts the value of the expression from character, numeric, or timestamp data to type date. The

## %DAYS (Number of Days)

**Syntax:**
```
%DAYS(number)
```

%DAYS converts a number into a duration that can be added to a date or timestamp value. %DAYS can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a date or timestamp. The result is a date or timestamp value with the appropriate number of days added or subtracted. For a date, the resulting value is in *ISO format. For an example of date and time arithmetic operations, see Figure 228 on page 716.

## %DEC (Convert to Packed Decimal Format)

**Syntax:**
```
%DEC(numeric or character expression{:precision:decimal places})
```
```
%DEC(date time or timestamp expression {:format})
```

%DEC converts the value of the first parameter to decimal (packed) format. Numeric or character expression When the first parameter is a numeric or character expression, the result has precision digits and decimal places decimal positions. The precision and decimal places must be numeric literals, named constants

## %DECH (Convert to Packed Decimal Format with Half Adjust)

**Syntax:**
```
%DECH(numeric or character expression :precision:decimal places )
```
```
%DECH (Convert to Packed Decimal Format with
```

%DECH is the same as %DEC except that if the expression is a decimal or float value, half adjust is applied to the value of the expression when converting to the desired precision. No message is issued if half adjust cannot be performed.. Unlike, %DEC, all three parameters are required. %DECH Examples

**Examples:**
```rpgle
D p7              s              7p 3 inz (1234.567)
D s9              s              9s 5 inz (73.73442)
D f8              s              8f   inz (123.456789)
D c15a            s             15a   inz (' 123.456789 -')
D c15b            s             15a   inz (' + 9 , 8 7 6 ')
D result1         s             15p 5
D result2         s             15p 5
D result3         s             15p 5
// using numeric parameters
result1 = %dec (p7) + 0.011;  // "result1" is now 1234.57800
result2 = %dec (s9 : 5: 0);   // "result2" is now   73.00000
result3 = %dech (f8: 5: 2);   // "result3" is now  123.46000
// using character parameters
result1 = %dec (c15a: 5: 2);  // "result1" is now -123.45
result2 = %dech(c15b: 5: 2);  // "result2" is now    9.88000
```

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %DECPOS (Get Number of Decimal Positions)

**Syntax:**
```
%DECPOS(numeric expression)
```

%DECPOS returns the number of decimal positions of the numeric variable or expression. The value returned is a constant, and so may participate in constant folding. The numeric expression must not be a float variable or expression.

**Examples:**
```rpgle
D p7              s              7p 3 inz (8236.567)
D s9              s              9s 5 inz (23.73442)
D result1         s              5i 0
D result2         s              5i 0
D result3         s              5i 0
result1 = %decpos (p7);     //  "result1" is now  3.
result2 = %decpos (s9);     //  "result2" is now  5.
result3 = %decpos (p7 * s9);//  "result3" is now  8.
```

## %DIFF

**Syntax:**
```
%DIFF (Difference Between Two Date, Time, or Timestamp Values)
```
```
%DIFF(op1 : op2 : unit {: frac })
```

The unit can be *MSECONDS, *SECONDS, *MINUTES, *HOURS, *DAYS, *MONTHS, or *YEARS. You can also use the following abbreviated forms of the unit: *MS, *S, *MN, *H, *D, *M, or *Y. %DIFF produces the difference (duration) between two date or time values. The first and second parameters must have the same, or compatible types. The following combinations are possible: • Date and date • Time and time • Timestamp and timestamp

## %DIV (Return Integer Portion of Quotient)

**Syntax:**
```
%DIV(n:m)
```

%DIV returns the integer portion of the quotient that results from dividing operands n by m. The two operands must be numeric values with zero decimal positions. If either operand is a packed, zoned, or binary numeric value, the result is packed numeric. If either operand is an integer numeric value, the result is integer. Otherwise, the result is unsigned numeric. Float numeric operands are not allowed. (See also “%REM (Return Integer Remainder)” on page 732.) If the operands are constants that can fit in 8-byte integer or unsigned fields, constant folding is applied to the built-in function. In this case, the %DIV built-in function can be coded in the definition specifications.

*For more information, see “Arithmetic Operations” on page 597 or “Built-in Functions” on page 590.*

## %EDITC (Edit Value Using an Editcode)

**Syntax:**
```
%EDITC(numeric : editcode {: *ASTFILL | *CURSYM | currency-symbol})
```

This function returns a character result representing the numeric value edited according to the edit code. In general, the rules for the numeric value and edit code are identical to those for editing numeric values in output specifications. The third parameter is optional, and if specified, must be one of: *ASTFILL Indicates that asterisk protection is to be used. This means that leading zeros are replaced with asterisks in the returned value. For example, %EDITC(-0012.5 : 'K' : *ASTFILL) returns '***12.5-'. *CURSYM Indicates that a floating currency symbol is to be used. The actual symbol will be the one specified on the control specification in the CURSYM keyword, or the default, '$'. When *CURSYM is specified, the currency symbol is placed in the the result just before the first significant digit. For example,

## %EDITFLT (Convert to Float External Representation)

**Syntax:**
```
%EDITFLT(numeric expression)
```

%EDITFLT converts the value of the numeric expression to the character external display representation of float. The result is either 14 or 23 characters. If the argument is a 4-byte float field, the result is 14 characters. Otherwise, it is 23 characters. If specified as a parameter to a definition specification keyword, the parameter must be a numeric literal, float literal, or numeric valued constant name or built-in function. When specified in an expression, constant folding is applied if the numeric expression has a constant value.

**Examples:**
```rpgle
D f8              s              8f   inz (50000)
D string          s             40a   varying
string = 'Float value is ' + %editflt (f8 - 4E4) + '.';
```

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %EDITW (Edit Value Using an Editword)

**Syntax:**
```
%EDITW(numeric : editword)
```

%EDITFLT (Convert to Float External Representation) Operations, Expressions, and Functions  681 This function returns a character result representing the numeric value edited according to the edit word. The rules for the numeric value and edit word are identical to those for editing numeric values in output specifications. Float expressions are not allowed in the first parameter. Use %DEC to convert a float to an editable format. The edit word must be a character constant. D amount          S             30A D salary          S              9P 2 D editwd          C                    '$ ,   ,  **Dollars&  &Cents' * If the value of salary is 2451.53, then the edited version of * (salary * 12) is '$***29,418*Dollars 36 Cents'. The value of * amount is 'The annual salary is $***29,418*Dollars 36 Cents'.

**Examples:**
```rpgle
amount = 'The annual salary is '
+ %editw(salary * 12 : editwd);
```

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %ELEM (Get Number of Elements)

**Syntax:**
```
%ELEM(table_name)
```
```
%ELEM(array_name)
```
```
%ELEM(multiple_occurrence_data_structure_name)
```
```
%ELEM(array_name:*ALLOC)
```
```
%ELEM(array_name:*KEEP)
```
```
%ELEM(array_name:*MAX)
```

%ELEM returns the number of elements in the specified array, table, or multiple-occurrence data structure. The value returned is in unsigned integer format (type U). It may be specified anywhere a numeric constant is allowed in the definition specification or in an expression in the extended factor 2 field. The parameter must be the name of an array, table, or multiple occurrence data structure. When the array has a variable dimension (the array is defined with DIM(*AUTO) or DIM(*VAR), %ELEM can be used in several additional ways. • %ELEM can be used as the target of an assignment statement to change the current number of elements of the varying-dimension array. • A second parameter can be specified for %ELEM when %ELEM is used for its value. *ALLOC The number of elements allocated to the array is returned. *MAX The maximum number of elements for the array is returned. • A second parameter can be specified for %ELEM when %ELEM is the target of an assignment statement. *ALLOC The number of elements allocated to the array is increased if the value on the right-hand side of the assignment statement is greater than the current number of elements of the array. The number of elements is not changed if the value is less than the current number of elements. The number of elements allocated to the array might be larger than the specified value. The current number of

## %EOF (Return End or Beginning of File Condition)

**Syntax:**
```
%EOF(filename) is not changed. %EOF with no parameter is not changed by these operations.
```

%EOF{(file_name)} %EOF returns '1' if the most recent read operation or write to a subfile ended in an end of file or beginning of file condition; otherwise, it returns '0'. The operations that set %EOF are: • “READ (Read a Record)” on page 914 • “READC (Read Next Changed Record)” on page 916 • “READE (Read Equal Key)” on page 917 • “READP (Read Prior Record)” on page 919 • “READPE (Read Prior Equal)” on page 921 • “WRITE (Create New Records)” on page 974 (subfile only). The following operations, if successful, set %EOF(filename) off. If the operation is not successful, • “CHAIN (Random Retrieval from a File)” on page 788 • “OPEN (Open File for Processing)” on page 907 • “SETGT (Set Greater Than)” on page 936 • “SETLL (Set Lower Limit)” on page 939 When a full-procedural file is specified, this function returns '1' if the previous operation in the list above, for the specified file, resulted in an end of file or beginning of file condition. For primary and secondary files, %EOF is available only if the file name is specified. It is set to '1' if the most recent input operation during *GETIN processing resulted in an end of file or beginning of file condition. Otherwise, it returns '0'. This function is allowed for input, update, and record-address files; and for display files allowing WRITE to subfile records. F*Filename+IPEASFRlen+LKlen+AIDevice+.Keywords+++++++++++++++++++++++++ * File INFILE has record format INREC FINFILE    IF   E             DISK

**Examples:**
```rpgle
READ INREC;  // read a record
IF  %EOF;
// handle end of file
ENDIF;
```

*For more information, see “File Operations” on page 616 or “Built-in Functions” on page 590.*

## %EQUAL (Return Exact Match Condition)

%EQUAL{(file_name)} %EQUAL returns '1' if the most recent relevant operation found an exact match; otherwise, it returns '0'. The operations that set %EQUAL are: • “SETLL (Set Lower Limit)” on page 939 • “LOOKUP (Look Up a Table or Array Element)” on page 858 If %EQUAL is used without the optional file_name parameter, then it returns the value set for the most recent relevant operation. For the SETLL operation, this function returns '1' if a record is present whose key or relative record number is equal to the search argument. For the LOOKUP operation with the EQ indicator specified, this function returns '1' if an element is found that exactly matches the search argument. If a file name is specified, this function applies to the most recent SETLL operation for the specified file. This function is allowed only for files that allow the SETLL operation code. For more examples, see Figure 327 on page 860 and Figure 371 on page 942.

*For more information, see “File Operations” on page 616, “Result Operations” on page 628, or “Built-in*

## %ERROR (Return Error Condition)

%ERROR returns '1' if the most recent operation with extender 'E' specified resulted in an error condition. This is the same as the error indicator being set on for the operation. Before an operation with extender 'E' specified begins, %ERROR is set to return '0' and remains unchanged following the operation if no error occurs. All operations that allow an error indicator can also set the %ERROR built-in function. The CALLP operation can also set %ERROR. For examples of the %ERROR built-in function, see Figure 245 on page 748 and Figure 246 on page 749. %FIELDS

*For more information, see “Result Operations” on page 628 or “Built-in Functions” on page 590.*

## %FIELDS (Fields to update)

**Syntax:**
```
%FIELDS(name{:name...})
```

A list of fields can be specified as the final argument to Input/Output operation UPDATE coded in a free-form group. Only the fields specified are updated into the Input/Output buffer. Note: 1. Each name must be the name of a field in the input buffer for the record. If the field is renamed, the internal name is used. 2. The name can be a subfield from a data structure defined with the EXTNAME/LIKEREC keyword using the file/format name of the record being updated. *INPUT must be specified with the keyword used and *NULL must not be specified. The name specified must contain the subfield name that corresponds to the input field. For a qualified data structure, the simple qualified name of the subfield is used. 3. The name can be a subfield of a data structure defined with the LIKEDS keyword of a data structure defined as described above. %FIELDS specifies a list of fields to update. For example:

## %FLOAT (Convert to Floating Format)

**Syntax:**
```
%FLOAT(numeric or character expression)
```

%FLOAT converts the value of the expression to float format. This built-in function may only be used in expressions. If the parameter is a character expression • See “Rules for converting character values to numeric values using built-in functions” on page 609 for the rules for character expressions for %DEC. • If invalid numeric data is found, an exception occurs with status code 105.

## %FOUND (Return Found Condition)

%FOUND{(file_name)} %FOUND returns '1' if the most recent relevant file operation found a record, a string operation found a match, or a search operation found an element. Otherwise, this function returns '0'. The operations that set %FOUND are: • File operations: – “CHAIN (Random Retrieval from a File)” on page 788 – “DELETE (Delete Record)” on page 816 – “SETGT (Set Greater Than)” on page 936 – “SETLL (Set Lower Limit)” on page 939 • String operations: – “CHECK (Check Characters)” on page 790 – “CHECKR (Check Reverse)” on page 792 – “SCAN (Scan String)” on page 932 Note: Built-in function %SCAN does not change the value of %FOUND. • Search operations: – “LOOKUP (Look Up a Table or Array Element)” on page 858 If %FOUND is used without the optional file_name parameter, then it returns the value set for the most recent relevant operation. When a file_name is specified, then it applies to the most recent relevant operation on that file. For file operations, %FOUND is opposite in function to the "no record found NR" indicator. For string operations, %FOUND is the same in function as the "found FD" indicator. For the LOOKUP operation, %FOUND returns '1' if the operation found an element satisfying the search conditions. For an example of %FOUND with LOOKUP, see Figure Figure 213 on page 686.

*For more information, see “File Operations” on page 616, “Result Operations” on page 628, or “Built-in*

## %GEN

**Syntax:**
```
%GEN (generator {: options})
```

%GEN is used as the third operand of the DATA-GEN operation code to specify the program or procedure to generate the document, and any options supported by the generator. %GEN does not return a value,

## %GRAPH (Convert to Graphic Value)

**Syntax:**
```
%GRAPH(char-expr | graph-expr | UCS-2-expr { : ccsid })
```

%GRAPH converts the value of the expression from character, graphic, or UCS-2 and returns a graphic value. The result is varying length if the parameter is varying length. The second parameter, ccsid, is optional and indicates the CCSID of the resulting expression. The CCSID defaults to the default graphic CCSID of the module as specified by control keyword CCSID(*GRAPH). If CCSID(*GRAPH : *IGNORE) is specified on the control specification or assumed for the module, the %GRAPH built-in is not allowed. If the parameter is a constant, the conversion will be done at compile time. In this case, the CCSID is the graphic CCSID related to the CCSID of the source file. If the parameter is character data with an EBCDIC CCSID, the character data must be in the form shift-out graphic-data shift-in For example, 'oAABBCCi'. See “Conversions” on page 292 for information about the possibility that converting some data to graphic may not be able to convert all the data successfully.

*For more information, see “Graphic Format” on page 283, “Conversion Operations” on page 608, or*

## %HANDLER

**Syntax:**
```
%HANDLER (handlingProcedure : communicationArea )
```
```
%HANDLER (handlingProcedure :
```
```
%HANDLER (handlingProcedure :
```
```
%HANDLER (handlingProcedure :
```

%HANDLER is used to identify a procedure to handle an event or a series of events. %HANDLER does not return a value, and it can only be specified as the first operand of XML-SAX, XML-INTO and DATA-INTO. The first operand, handlingProcedure specifies the prototype of the handling procedure. The return value and parameters specified by the prototype, or by the procedure interface if the prototype is not explicitly specified, must match the parameters required for the handling procedure; the requirements are determined by the operation that %HANDLER is specified for. See “XML-SAX (Parse an XML Document)” on page 1016, “XML-INTO (Parse an XML Document into a Variable)” on page 977 or “DATA-INTO (Parse a Document into a Variable)” on page 803 for the specific requirements for the definition of the handling procedures. The second operand, communicationArea, specifies a variable to be passed as a parameter on every call to the handling procedure. The operand must be an exact match for the first prototyped parameter of the handling procedure, according to the same rules that are used for checking prototyped parameters passed by reference. The communication-area parameter can be any type, including arrays and data structures. When an operation code uses the %HANDLER built-in function, the following sequence of events occurs: 1. The operation using the %HANDLER built-in function begins. 2. When an event occurs during the operation that must be handled by the handling procedure, the RPG runtime calls the handling procedure specified as the first operand of %HANDLER. The first parameter passed to the handling procedure is the communication area that was specified as the second operand of %HANDLER. The other parameters depend on the operation and the nature of the event that occurred. communicationArea ) Operations, Expressions, and Functions  693 3. The handling procedure processes the parameters, possibly updating the communication-area parameter. 4. The handling procedure returns a zero if it completed successfully, and a non-zero value if it did not complete successfully. 5. If the returned value was zero, the RPG runtime continues processing until either the operation is complete, or another event occurs. If the returned value was not zero, the operation ends. 6. If another event occurs, the handling procedure is called again. If the previous call to the handling procedure changed the communication area, the changes can be seen on subsequent calls. 7. When the operation is complete, control passes to the statement following the operation that used the %HANDLER built-in function. If the handling procedure changed the communication area, the changes can be seen in the procedure that used the %HANDLER built-in function. The communication area can be used for several purposes. 1. To communicate information from the procedure coding the %HANDLER built-in function to the handling procedure. 2. To communicate information from the handling procedure back to the procedure coding the %HANDLER built-in function. 3. To keep state information between successive calls of the handling procedure. State information can also be kept in static variables in the handling procedure, but when static variables are used, incorrect results can occur if the handling procedure has been enabled by more than one %HANDLER operation. By using a communication area parameter, the usages of the handling procedure are independent from each other. communicationArea ) * Data structure used as a parameter between * the XML-SAX operation and the handling * procedure. *   - "attrName" is set by the procedure doing the *     XML-SAX operation and used by the handling procedure *   - "attrValue" is set by the handling procedure *     and used by the procedure doing the XML-SAX *     operation *   - "haveAttr" is used internally by the handling *     procedure D info            DS D   attrName                    20A    VARYING D   haveAttr                      N D   attrValue                   20A    VARYING * Prototype for procedure "myHandler" defining * the communication-area parameter as being * like data structure "info" D myHandler       PR            10I 0 D   commArea                           LIKEDS(info) D   event                       10I 0  VALUE D   string                        *    VALUE D   stringLen                   20I 0  VALUE D   exceptionId                 10I 0  VALUE /free // The purpose of the following XML-SAX operation // is to obtain the value of the first "companyname" // attribute found in the XML document. // The communication area "info" is initialized with // the name of the attribute whose value is // to be obtained from the XML document. attrName = 'companyname'; // Start SAX processing.  The procedure "myHandler" // will be called for every SAX event; the first // parameter will be the data structure "info". xml-sax(e) %handler(myHandler : info) %xml(xmldoc); // The XML-SAX operation is complete.  The // communication area can be checked to get the // value of the attribute. if not %error() and attrValue <> ''; dsply (attrName + '=' + attrValue); endif; : : * The SAX handling procedure "myHandler" P myHandler       B D                 PI            10I 0 D   comm                               LIKEDS(info) D   event                       10I 0  VALUE D   string                        *    VALUE D   stringLen                   20I 0  VALUE D   exceptionId                 10I 0  VALUE D value           S          65535A    VARYING D                                      BASED(string) D ucs2value       S          16383C    VARYING D                                      BASED(string) D rc              S             10I 0  INZ(0) /free select;

## %HIVAL (Highest Value)

**Syntax:**
```
%HIVAL(variable)
```
```
%HIVAL(variable)
```

%HIVAL returns the highest value for the variable or enumeration. See “%HIVAL and %LOVAL (Highest Value or Lowest Value)” on page 696 for detailed information about both %HIVAL and %LOVAL. %HIVAL and %LOVAL (Highest Value or Lowest Value) %LOVAL(variable) %HIVAL returns the highest value for the variable. %LOVAL returns the lowest value for the variable.

## %HOURS (Number of Hours)

**Syntax:**
```
%HOURS(number)
```

%HOURS converts a number into a duration that can be added to a time or timestamp value. %HOURS can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a time or timestamp. The result is a time or timestamp value with the appropriate number of hours added or subtracted. For a time, the resulting value is in *ISO format. For an example of date and time arithmetic operations, see Figure 228 on page 716.

## %INT (Convert to Integer Format)

**Syntax:**
```
%INT(numeric or character expression)
```

%INT converts the value of the expression to integer. Any decimal digits are truncated. This built-in function may only be used in expressions. %INT can be used to truncate the decimal positions from a float or decimal value allowing it to be used as an array index. If the parameter is a character expression • See “Rules for converting character values to numeric values using built-in functions” on page 609 for the rules for character expressions for %DEC. • Floating point data, for example '1.2E6', is not allowed. • Floating point data is not allowed. That is, where the numeric value is followed by E and an exponent, for example '1.2E6'. • If invalid numeric data is found, an exception occurs with status code 105

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %INTH (Convert to Integer Format with Half Adjust)

**Syntax:**
```
%INTH(numeric or character expression)
```

%INTH is the same as %INT except that if the expression is a decimal, float or character value, half adjust is applied to the value of the expression when converting to integer type. No message is issued if half adjust cannot be performed.

## %KDS (Search Arguments in Data Structure)

**Syntax:**
```
%KDS(data-structure-name{:num-keys})
```

%KDS is allowed as the search argument for any keyed Input/Output operation (CHAIN, DELETE, READE, READPE, SETGT, SETLL) coded in a free-form group. The search argument is specified by the subfields of the data structure name coded as the first argument of the built-in function. The key data structure may be (but is not limited to), an externally described data structure with keyword EXTNAME(...:*KEY) or LIKEREC(...:*KEY).. Note: 1. The first argument must be the name of a data structure. This includes any subfield defined with keyword LIKEDS or LIKEREC. 2. The second argument specifies how many of the subfields to use as the search argument. It can be a constant, a variable, or an expression. 3. The individual key values in the compound key are taken from the top level subfields of the data structure. Subfields defined with LIKEDS are considered character data. 4. Subfields used to form the compound key must not be arrays. 5. The types of all subfields (up to the number specified by "num-keys") must match the types of the actual keys. Where lengths, formats and CCSIDs differ, the value is converted. See “*STRICTKEYS” on page 372 for information about the effect Control keyword EXPROPTS(*STRICTKEYS) has on the rules for specifying keys with %KDS. 6. If the data structure is defined as an array data structure (using keyword DIM), an index must be supplied for the data structure. 7. Opcode extenders H, M, or R specified on the keyed Input/Output operations code affect the moving of the search argument to the corresponding position in the key build area.

## %LEFT (Get Leftmost Characters)

**Syntax:**
```
%LEFT(string : length { : *NATURAL | *STDCHARSIZE } )
```

%LEFT returns the leftmost characters of a string. The first operand is a string. It can be alphanumeric, graphic, or UCS-2. The second operand is the number of characters to return. The third operand can be specified to set the CHARCOUNT mode for the statement. See “Example demonstrating the effect of the CHARCOUNT mode on %LEFT” on page 701. • Specify *NATURAL as the third parameter for %LEFT to operate in CHARCOUNT NATURAL mode. • Specify *STDCHARSIZE as the third parameter for %LEFT to operate in CHARCOUNT STDCHARSIZE mode. • If the third parameter is not specified, %LEFT operates in the charcount mode for the statement as set by the CHARCOUNT Control keyword or the /CHARCOUNT directives. When %LEFT is operating in CHARCOUNT NATURAL mode, the second operand refers to the number of characters to return. When %LEFT is operating in CHARCOUNT STDCHARSIZE mode, the second operand is the number of bytes or double-bytes to return. Example of %LEFT In the following example, the string begins with a name followed by a colon followed by more information. The programmer uses %LEFT to get the name. 1. The %SCAN built-in function returns 11 for the position of the colon.

*For more information, see “String Operations” on page 628 and “Built-in Functions” on page 590.*

## %LEN (Get or Set Length)

**Syntax:**
```
%LEN(expression)
```
```
%LEN(varying-length expression : *MAX)
```

%LEN can be used to get the length of a variable expression, to set the current length of a variable-length field, or to get the maximum length of a varying-length expression. The parameter must not be a figurative constant. Note: • For alphanumeric data, %LEN always returns the number of bytes. • For UCS-2 fields and graphic data, %LEN always returns the number of double bytes. If you want to know the number of natural characters in the expression, use %CHARCOUNT instead. For example, the UTF-8 value 'ábç' has five bytes, but three characters. DCL-S fld1 VARCHAR(10) INZ('ábç'); DCL-S n INT(10);

**Examples:**
```rpgle
n = %LEN(fld1);
// n = 5
n = %CHARCOUNT(fld1);
```

## %LIST

**Syntax:**
```
%LIST (item { : item { : item ... } } )
```

%LIST returns a temporary array whose elements have the values of the items listed in its operands. %LIST can be used in calculation expressions wherever an array can be used except: • SORTA • %ELEM • %LOOKUP

## %LOVAL (Lowest Value)

**Syntax:**
```
%LOVAL(variable)
```

%LOVAL returns the lowest value for the variable or enumeration. See “%HIVAL and %LOVAL (Highest Value or Lowest Value)” on page 696 for detailed information about

## %LOWER (Convert to Lower Case)

**Syntax:**
```
%LOWER(string {: start { : length { : *NATURAL | *STDCHARSIZE } } })
```

%LOWER returns the string operand converted to lower case. See “%LOWER and %UPPER (Convert to Lower or Upper Case)” on page 708 for detailed information about both %LOWER and %UPPER. %LOWER and %UPPER (Convert to Lower or Upper Case)

## %MAX (Maximum Value)

**Syntax:**
```
%MAX(item1 : item2 {: item3 { item4 ... } })
```

%MAX returns the maximum value of its operands. See “%MAX and %MIN (Maximum or Minimum Value)” on page 711 for detailed information about both

## %MAXARR (Maximum Element in an Array)

**Syntax:**
```
%MAXARR(array {: start-index {:number-of-elements}})
```

%MAXARR returns the index of the maximum value in the array, or the subsection of the array identified by the start-element operand and the number-of-elements operand. See “%MAXARR and %MINARR (Maximum or Minimum Element in an Array)” on page 712 for detailed

## %MIN (Minimum Value)

**Syntax:**
```
%MIN(item1 : item2 {: item3 { item4 ... } })
```

%MIN returns the minimum value of its operands. See “%MAX and %MIN (Maximum or Minimum Value)” on page 711 for detailed information about both

## %MINARR (Minimum Element in an Array)

**Syntax:**
```
%MINARR(array {: start-index {:number-of-elements}})
```

%MINARR returns the index of the minimum value in the array, or the subsection of the array identified by the start-element operand and the number-of-elements operand. See “%MAXARR and %MINARR (Maximum or Minimum Element in an Array)” on page 712 for detailed information about both %MAXARR and %MINARR. %MAX and %MIN (Maximum or Minimum Value) %MAX(item1 : item2 {: item3 { item4 ... } }) %MIN(item1 : item2 {: item3 { item4 ... } }) %MAX returns the maximum value of its operands and %MIN returns the minimum value of its operands. Otherwise, the rules and behavior of these built-in functions are identical. The operands must all have data types that are compatible for comparison with each other. For example, if one item in the list is alphanumeric, the other items can be alphanumeric, UCS-2, or graphic. If one item is packed numeric, the other items can be packed numeric, zoned numeric, integer, unsigned integer, binary decimal, or float. Items with type procedure-pointer or type object are not allowed as operands. There must be at least two operands. There is no practical upper limit for the number of operands. When the built-in function is used in a Declaration statement, there must be exactly two operands; the operands must both be numeric and they cannot be float or hexadecimal. If any decimal operand has more decimal positions than the result of the operation, half-adjust is used. If the built-in function is used in a Declaration statement, the result is the operand with the higher (%MAX) or lower (%MIN) value. The data type of the value returned by the built-in function in calculations depends on the data type of the operands. See “Determining the Common Type of Multiple Operands” on page 715. To find the maximum or minimum value in an array, use %MAXARR or %MINARR. Examples of %MAX and %MIN 1. %MAX used in a Declaration statement. The dimension of arr3 is 5, the maximum of the dimension of arr1 and arr1. DCL-S arr1 CHAR(10) DIM(3); DCL-S arr2 CHAR(10) DIM(5); DCL-S arr3 CHAR(10) DIM(%MAX(%ELEM(arr1) : %ELEM(arr2))); 2. %MIN used in a Calculation statement. DCL-S triangleArea PACKED(7 : 2); DCL-S squareArea PACKED(7 : 2); DCL-S circleArea PACKED(5 : 2); DCL-S size PACKED(7 : 2);

## %MINUTES (Number of Minutes)

**Syntax:**
```
%MINUTES(number)
```

%MINUTES converts a number into a duration that can be added to a time or timestamp value. %MINUTES can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a time or timestamp. The result is a time or timestamp value with the appropriate number of minutes added or subtracted. For a time, the resulting value is in *ISO format. For an example of date and time arithmetic operations, see Figure 228 on page 716.

## %MONTHS (Number of Months)

**Syntax:**
```
%MONTHS(number)
```

%MONTHS converts a number into a duration that can be added to a date or timestamp value. %MONTHS can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a date or timestamp. The result is a date or timestamp value with the appropriate number of months added or subtracted. For a date, the resulting value is in *ISO format. In most cases, the result of adding or subtracting a given number of months is obvious. For example, 2000-03-15 + %MONTHS(1) is 2000-04-15. If the addition or subtraction would produce a nonexistent date (for example, February 30), the last day of the month is used instead. Adding or subtracting a number of months to the 29th, 30th, or 31st day of a month may not be reversible. For example, 2000-03-31 + %MONTHS(1) - %MONTHS(1) is 2000-03-30. “Unexpected Results” on page 614.

**Examples:**
```rpgle
// Determine the date in 3 years
newdate = date + %YEARS(3);
// Determine the date in 6 months prior
loandate = duedate - %MONTHS(6);
// Construct a timestamp from a date and time
duestamp = duedate + t'12.00.00';
```

*For more information, see “Date Operations” on page 612, “Built-in Functions” on page 590, and*

## %MSECONDS (Number of Microseconds)

**Syntax:**
```
%MSECONDS(number)
```

%MSECONDS converts a number into a duration that can be added to a time or timestamp value. %MSECONDS can only follow the plus or minus sign in an addition or subtraction expression. The value

## %MSG

**Syntax:**
```
%MSG (message-id : message-file { : replacement-text } )
```
```
%MSG (message-id : message-file { : replacement
```

%MSG is used as the second operand of the SND-MSG operation. %MSG does not return a value, and it cannot be specified anywhere other than for the SND-MSG operation. %MSG specifies the message to send. The first operand is the message ID. It must be a character expression in the job CCSID. The message ID is 7 characters long. If the length of the operand is longer than 7, the remaining characters must be blank. At run-time, the message ID must exist in the message file. The second operand is the message file. It must be a character expression in the job CCSID. It can be in one of the following forms: • MYMSGF • MYLIB/MYMSGF • *LIBL/MYMSGF The third operand is optional. It specifies the replacement text for the message. It can be a character value in the job CCSID or a data structure.

## %NULLIND (Query or Set Null Indicator)

**Syntax:**
```
%NULLIND(fieldname)
```

The %NULLIND built-in function can be used to query or set the null indicator for null-capable fields. This built-in function can only be used if the ALWNULL(*USRCTL) keyword is specified on a control specification or as a command parameter. The fieldname can be a null-capable array element, data structure, stand-alone field, subfield, or multiple occurrence data structure. %NULLIND can only be used in expressions in extended factor 2. When used on the right-hand side of an expression, this function returns the setting of the null indicator for the null-capable field. The setting can be *ON or *OFF. When used on the left-hand side of an expression, this function can be used to set the null indicator for null-capable fields to *ON or *OFF. The content of a null-capable field remains unchanged. See “Database Null Value Support” on page 321 for more information on handling records with null capable fields and keys. 590. * Test the null indicator for a null-capable field.

**Examples:**
```rpgle
if  %nullind (fieldname1);
// field is null
endif;
```
```rpgle
// Set the null indicator for a null-capable field.
%nullind(fieldname1) = *ON;
%nullind (fieldname2) = *OFF;
```

*For more information, see “Indicator-Setting Operations” on page 619 or “Built-in Functions” on page*

## %OCCUR (Set/Get Occurrence of a Data Structure)

**Syntax:**
```
%OCCUR(dsn-name)
```
```
%OCCUR(mds) = 7;
```

%OCCUR gets or sets the current position of a multiple-occurrence data structure. When this function is evaluated for its value, it returns the current occurrence number of the specified data structure. This is an unsigned numeric value. When this function is specified on the left-hand side of an EVAL statement, the specified number becomes the current occurrence number. This must be a non-float numeric value with zero decimal places. Exception 00122 is issued if the value is less than 1 or greater than the total number of occurrences. “OCCUR (Set/Get Occurrence of a Data Structure)” on page 899. D mds             DS                  OCCURS(10)

**Examples:**
```rpgle
n = %OCCUR(mds);
// n = 1
n = %OCCUR(mds);
// n = 7
```

*For more information about multiple-occurrence data structures and the OCCUR operation code, see*

## %OMITTED (Return Parameter-Omitted Condition)

%OMITTED returns the *ON if the specified parameter was passed to the program or procedure and *OMIT was passed as the parameter. The operand for %OMITTED is the name of a parameter defined as part of the procedure interface for the current procedure. Use %PASSED if you want to know whether the parameter is available to be used in the procedure. Use %OMITTED if you want to know that *OMIT was passed for the parameter. Note: • *OMIT must be specified for the OPTIONS of the procedure interface for the specified parameter. • A parameter defined using a *ENTRY PLIST cannot be specified as the operand for %OMITTED. • A parameter specified in the procedure interface for the main procedure cannot be specified as the operand for %OMITTED in different procedure. • The parameter must be specified the same way it appears in the procedure interface parameter list. If the parameter is an array, an index cannot be specified. If the parameter is a data structure, a subfield cannot be specified. If the parameter is a file, a record format cannot be specified. Note: If the passed parameter is based on a pointer and the pointer is null, %OMITTED returns *ON. With OPTIONS(*OMIT), when the address of the parameter is null, it appears as though *OMIT was passed.

*For more information, see “Built-in Functions” on page 590.*

## %OPEN (Return File Open Condition)

**Syntax:**
```
%OPEN(file_name)
```

%OPEN returns '1' if the specified file is open. A file is considered "open" if it has been opened by the RPG module during initialization or by an OPEN operation, and has not subsequently been closed. If the file is conditioned by an external indicator and the external indicator was off at module initialization, the file is considered closed, and %OPEN returns '0'. F*Filename+IPEASFRlen+LKlen+AIDevice+.Keywords+++++++++++++++++++++++++ * The printer file is opened in the calculation specifications FQSYSPRT   O    F  132        PRINTER USROPN

**Examples:**
```rpgle
// Open the file if it is not already open
if not %open (QSYSPRT);
open QSYSPRT;
endif;
```

*For more information, see “File Operations” on page 616 or “Built-in Functions” on page 590.*

## %PADDR (Get Procedure Address)

**Syntax:**
```
%PADDR(string|prototype)
```

%PADDR returns a value of type procedure pointer. The value is the address of the entry point identified by the argument. %PADDR may be compared with and assigned to only items of type procedure pointer. The parameter to %PADDR must be a character constant or a prototype name. If the prototype for a procedure is implicitly defined from its procedure interface, the prototype name is the same as the procedure name. The character constant can be a character or hexadecimal literal or constant name that represents a character or hexadecimal literal. When a character constant is used, this identifies the entry point by

## %PARMNUM (Return Parameter Number)

**Syntax:**
```
%PARMNUM(P2) will return 3.
```

%PARMNUM returns the number of the parameter in the parameter list. The operand for %PARMNUM is the name of a parameter defined as part of a procedure interface. Note: 1. A parameter defined using a *ENTRY PLIST cannot be specified as the operand for %PARMNUM. 2. The parameter must be specified the same way it appears in the procedure interface parameter list. If the parameter is an array, an index cannot be specified. If the parameter is a data structure, a subfield cannot be specified. If the parameter is a file, a record format cannot be specified. 3. If the RTNPARM keyword is coded for a procedure, the return value is handled as an additional first parameter. The other parameters have a number one higher than the apparent number. For example, if a procedure defined with RTNPARM has two parameters P1 and P2, %PARMNUM(P1) will return 2 and

## %PARMS (Return Number of Parameters)

%PARMS returns the number of parameters that were passed to the procedure in which %PARMS is used. For a cycle-main procedure, %PARMS is the same as *PARMS in the program status data structure. When %PARMS is used in a procedure that was called by a bound call, the value returned by %PARMS is not available if the calling program or procedure does not pass a minimal operational descriptor. The ILE RPG compiler always passes one, but other languages might not. If the caller is written in another ILE language, it will need to pass an operational descriptor on the call. If the operational descriptor is not passed, the value returned by %PARMS cannot be trusted. The value returned by %PARMS will be -1 if the system can determine that the operational descriptor was not passed, but in some cases when the system cannot detect this, the value returned by %PARMS may be an incorrect value that is zero or greater. The value returned by %PARMS includes the additional first parameter that is used to handle the the return value when the RTNPARM keyword is specified. For more information, see “RTNPARM” on page 517.

## %PARSER

**Syntax:**
```
%PARSER (parser {: options})
```

%PARSER is used as the third operand of the DATA-INTO operation code to specify the program or procedure to do the parsing, and any options supported by the parser. %PARSER does not return a value, and it cannot be specified anywhere other than for the DATA-INTO operation code. The first operand specifies the program or procedure to do the parsing. It can be • A procedure pointer expression • The %PADDR built-in function • A character expression identifying a program. It must be in one of the following forms

## %PASSED (Return Parameter-Passed Condition)

%PASSED returns the *ON if the specified parameter was passed to the program or procedure and *OMIT was not passed as the parameter. The operand for %PASSED is the name of a parameter defined as part of the procedure interface for the current procedure. Note: • For a procedure call, the caller must pass at least a minimal operational descriptor. See “%PARMS (Return Number of Parameters)” on page 723. • Either *NOPASS or *OMIT must be specified for the OPTIONS of the procedure interface for the specified parameter. • A parameter defined using a *ENTRY PLIST cannot be specified as the operand for %PASSED. • A parameter specified in the procedure interface for the main procedure cannot be specified as the operand for %PASSED in different procedure. • The parameter must be specified the same way it appears in the procedure interface parameter list. If the parameter is an array, an index cannot be specified. If the parameter is a data structure, a subfield cannot be specified. If the parameter is a file, a record format cannot be specified. Warning: If the passed parameter is based on a pointer and the pointer is null, the result of %PASSED depends on whether OPTIONS(*OMIT) is specified for the parameter. • If OPTIONS(*OMIT) is specified for the parameter, %PASSED returns *OFF. With OPTIONS(*OMIT), when the address of the parameter is null, it appears as though *OMIT was passed. • If OPTIONS(*OMIT) is not specified for the parameter, %PASSED returns *ON. When OPTIONS(*OMIT) is not specified, the address of the parameter is not checked for %PASSED.

*For more information, see “Built-in Functions” on page 590.*

## %PROC (Return Name of Current Procedure)

**Syntax:**
```
%PROC({*OWNER | *ONEXIT})
```

%PROC returns the external name of the current procedure. %PROC can only be specified in calculation statements. A parameter is not allowed for %PROC unless it specified in the ON-EXIT section of a procedure. In the ON-EXIT section of a procedure, a parameter is required. • %PROC(*OWNER) returns the external name of the procedure containing the ON-EXIT section. • %PROC(*ONEXIT) returns the external name of the procedure that implements the ON-EXIT section. The value returned by %PROC depends on where it is specified: • For a cycle-main procedure, the external name of the procedure is the name of the module when it was compiled. • For a linear-main procedure, the external name of the procedure is the uppercase form of name of the main procedure. See  1  in the example below. • For a subprocedure where EXTPROC was not specified, the external name of the procedure is the uppercase form of the name of the procedure. See  2  in the example below. • For a subprocedure where EXTPROC was specified, the external name of the procedure is the value specified by EXTPROC. See  3  in the example below. • For a Java procedure, the external name of the procedure is in the form "Java_classname_methodname". See  4  in the example below. • For examples of %PROC(*OWNER), see  5  in the procedures below.

## %RANGE

**Syntax:**
```
%RANGE (lower-limit : upper-limit)
```

%RANGE is used with the IN operator. %RANGE does not return a value, and it cannot be specified anywhere other than following the IN operator. When the IN operator is used with %RANGE, it determines whether the first operand is in the range specified by %RANGE. The expression using the IN operator with %RANGE is true if the first operand of the IN operator is greater than or equal to the first operand of %RANGE and less than or equal to the second operand of %RANGE. The first operand of the IN operator cannot be an array. The operands of %RANGE must be able to be compared to each other and to the first operand of the IN operator. For example, if the first operand of the IN operator has type date, the operands of %RANGE must also have type date.

## %REALLOC (Reallocate Storage)

**Syntax:**
```
%REALLOC(ptr:num)
```

%REALLOC changes the heap storage pointed to by the first parameter to be the length specified in the second parameter. The heap storage pointed to by the returned pointer has the same value as the heap storage pointed to by ptr. If the new length is longer than the old length, the additional storage is uninitialized. The first parameter must be a basing pointer value. The second parameter must be a non-float numeric value with zero decimal places. The length specified must be between 1 and the maximum size allowed. The maximum size allowed depends on the type of heap storage used for RPG memory management operations due to the ALLOC keyword on the Control specification. If the module uses teraspace heap storage, the maximum size allowed is 4294967295 bytes. Otherwise, the maximum size allowed is 16776704 bytes. The maximum size available at runtime may be less than the maximum size allowed by RPG. The function returns a pointer to the allocated storage. This may be the same as ptr or different. If the %REALLOC function is successful, the original pointer value specified in the first operand should not be used. When RPG memory management operations for the module are using single-level heap storage due to the ALLOC keyword on the Control specification, the %REALLOC built-in function can only handle pointers to single-level heap storage. When RPG memory management operations for the module are using teraspace heap storage, the %REALLOC built-in function operation can handle pointers to both single-level and teraspace heap storage.

*For more information, see “Memory Management Operations” on page 621.*

## %REM (Return Integer Remainder)

**Syntax:**
```
%REM(n:m)
```
```
%REM(A:B) = A - (%DIV(A:B) * B)
```

%REM returns the remainder that results from dividing operands n by m. The two operands must be numeric values with zero decimal positions. If either operand is a packed, zoned, or binary numeric value, the result is packed numeric. If either operand is an integer numeric value, the result is integer. Otherwise, the result is unsigned numeric. Float numeric operands are not allowed. The result has the same sign as the dividend. (See also “%DIV (Return Integer Portion of Quotient)” on page 679.) %REM and %DIV have the following relationship: If the operands are constants that can fit in 8-byte integer or unsigned fields, constant folding is applied to the built-in function. In this case, the %REM built-in function can be coded in the definition specifications. D A               S             10I 0 INZ(123) D B               S             10I 0 INZ(27) D DIV             S             10I 0 D REM             S             10I 0 D E               S             10I 0

**Examples:**
```rpgle
DIV = %DIV(A:B);  // DIV is now 4
REM = %REM(A:B);  // REM is now 15
E = DIV*B + REM;  // E is now 123
```

*For more information, see “Arithmetic Operations” on page 597 or “Built-in Functions” on page 590.*

## %REPLACE (Replace Character String)

**Syntax:**
```
%REPLACE(replacement string: source string{:start position {:source
```

length to replace { : *NATURAL | *STDCHARSIZE}}}) %REPLACE returns the character string produced by inserting a replacement string into the source string,

## %RIGHT (Get Rightmost Characters)

**Syntax:**
```
%RIGHT(string : length { : *NATURAL | *STDCHARSIZE } )
```

%RIGHT returns the rightmost characters of a string. The first operand is a string. It can be alphanumeric, graphic, or UCS-2. The second operand is the number of characters to return. The third operand can be specified to set the CHARCOUNT mode for the statement. See “Example demonstrating the effect of the CHARCOUNT mode on %RIGHT” on page 735. • Specify *NATURAL as the third parameter for %RIGHT to operate in CHARCOUNT NATURAL mode. • Specify *STDCHARSIZE as the third parameter for %RIGHT to operate in CHARCOUNT STDCHARSIZE mode. • If the third parameter is not specified, %RIGHT operates in the charcount mode for the statement as

## %SCAN (Scan for Characters)

**Syntax:**
```
%SCAN(search argument : source string {: start position {: length {: *NATURAL | *STDCHARSIZE}}})
```

%SCAN returns the first position of the search argument in the source string, or 0 if it was not found. The start position and length specify the substring of the source string to be searched. The start position defaults to 1 and the length defaults to the remainder of the source string. The result is always the

## %SCANR (Scan Reverse for Characters)

**Syntax:**
```
%SCANR(search argument : source string {: start position {: length {: *NATURAL |
```

*STDCHARSIZE}}}) %SCANR returns the last position of the search argument in the source string, or 0 if it was not found. The start position and length specify the substring of the source string to be searched. The start position defaults to 1 and the length defaults to the remainder of the source string. The result is always the position in the source string even if the starting position is specified. The first and second parameters must be of type character, graphic, or UCS-2. If the first parameter does not have the same type or CCSID as the second parameter, the first parameter is converted to the type and CCSID of the second parameter. The third and fourth parameters, if specified, must be numeric with zero decimal positions. The third, fourth, or fifth parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %SCANR operates in CHARCOUNT NATURAL mode. The start position, length, and return value are measured in characters rather than bytes or double bytes. For example, if the source string is a UTF-8 string with the value 'ábç12', a start position of 3 refers to 'ç' because it is the third character. • Specify *STDCHARSIZE to indicate that %SCANR operates in CHARCOUNT STDCHARSIZE mode. In the previous example, with CHARCOUNT STDCHARSIZE mode, a start position of 3 refers to 'b' because it is

## %SCANRPL (Scan and Replace Characters)

**Syntax:**
```
%SCANRPL(scan string : replacement : source { : scan start  { : scan length { : *NATURAL |
```

## %SECONDS (Number of Seconds)

**Syntax:**
```
%SECONDS(number)
```

%SECONDS converts a number into a duration that can be added to a time or timestamp value. %SECONDS can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a time or timestamp. The result is a time or timestamp value with the appropriate number of seconds added or subtracted. For a time, the resulting value is in *ISO format. If you are adding or subtracting %SECONDS from a timestamp value, the parameter can have decimal places specifying the number of fractional seconds to add or subtract. For example, the following example adds 5.72 seconds to the timestamp.

**Examples:**
```rpgle
timestamp2 = timestamp1 + %SECONDS(5.72);
```

## %SHTDN (Shut Down)

## %SIZE (Get Size in Bytes)

**Syntax:**
```
%SIZE(variable)
```
```
%SIZE(literal)
```
```
%SIZE(array{:*ALL})
```
```
%SIZE(table{:*ALL})
```
```
%SIZE(multiple-occurrence data structure{:*ALL})
```

%SIZE returns the number of bytes occupied by the constant or field. The argument may be a literal, a named constant, a data structure, a data structure subfield, a field, an array or a table name. It cannot contain an expression, but some constant-valued built-in functions and constant expressions may be accepted. The value returned is in unsigned integer format (type U). For a graphic literal, the size is the number of bytes occupied by the graphic characters, not including leading and trailing shift characters. For a hexadecimal or UCS-2 literal, the size returned is half the number of hexadecimal digits in the literal. For variable-length fields, %SIZE returns the total number of bytes occupied by the field (two or four bytes longer than the declared maximum length). The length returned for a null-capable field (%SIZE) is always its full length, regardless of the setting of its null indicator. Note the following considerations for %SIZE when the argument is an array name, table name, or multiple-occurrence data structure name. • The value returned is the size of one element or occurrence. • If *ALL is specified as the second parameter for %SIZE, the value returned is the storage taken up by all elements or occurrences. • The alignment of a data structure is the largest alignment that is required by the subfields of the data structure. If ALIGN(*FULL) is specified, then the size of each element of the data structure is a multiple of its alignment. If ALIGN is specified without a parameter, or if the ALIGN keyword is not specified, and the data structure contains at least one pointer, then the size that is occupied by the data structure might be less than a multiple of its alignment. See “ALIGN{(*FULL)}” on page 449 for more information. • For a multiple-occurrence data structure or data structure array that contains pointer subfields, the size that is occupied by the entire data structure might be greater than the size of one occurrence times the number of occurrences. The system requires that pointers be 16-byte aligned; that is, they must be placed in storage at addresses evenly divisible by 16. As a result, the length of each occurrence might have to be increased enough to make the length an exact multiple of 16 so that the pointer subfields will be positioned correctly in storage for every occurrence. Similarly, if the ALIGN keyword is specified, float, integer and unsigned integer subfields are positioned within the data structure at addresses evenly divisible by the size of the subfield. To ensure that the size of the entire data structure

## %SPLIT (Split String into Substrings)

**Syntax:**
```
%SPLIT(string {: separators { : *ALLSEP {: *NATURAL | *STDCHARSIZE}}})
```

%SPLIT splits a string into an array of substrings. It returns a temporary array of the substrings. %SPLIT can be used in calculation statements wherever an array can be used except: • SORTA • %ELEM • %LOOKUP • %SUBARR The first operand is the string to be split. It can be alphanumeric, graphic, or UCS-2. The second operand is the list of characters that indicate the end of each substring. It is optional unless *ALLSEP is specified as the third parameter. • If it is not specified, or if *BLANK or *BLANKS is specified, %SPLIT splits at blanks. • If it is specified and not *BLANK or *BLANKS: – If it does not have the same type or CCSID as the first operand, it is converted to the type and CCSID of the first operand. – If the length of the second operand is greater than 1, any of the characters in the second operand indicate the end of each substring. For example, %SPLIT('abc.def-ghi' : '.-') has two separator characters, '.', and '-', so it returns an array with three elements: ('abc','def','ghi'). The third operand can be *ALLSEP, indicating that every separator is considered to separate two substrings. When *ALLSEP is not specified, separators following other separators, leading separators, and trailing separators are ignored. The final parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %SPLIT operates in CHARCOUNT NATURAL mode. The number of bytes in each character is considered when locating the separators. For example, if the string parameter is a UTF-8 string with the value '1á2ç3', and the separators parameter is a UTF-8 string with the value 'á' only 'á' is considered to be a separator. The result strings are '1' and '2ç3'. • Specify *STDCHARSIZE to indicate that %SPLIT operates in CHARCOUNT STDCHARSIZE mode. In the previous example, with CHARCOUNT STDCHARSIZE mode, each byte of the separator is considered

## %SQRT (Square Root of Expression)

**Syntax:**
```
%SQRT(numeric expression)
```

%SQRT returns the square root of the specified numeric expression. If the operand is of type float, the result is of type float; otherwise, the result is packed decimal numeric. If the parameter has a value less

## %STATUS (Return File or Program Status)

%STATUS{(file_name)} %STATUS returns the most recent value set for the program or file status. %STATUS is set whenever the program status or any file status changes, usually when an error occurs. If %STATUS is used without the optional file_name parameter, then it returns the program or file status most recently changed. If a file is specified, the value contained in the INFDS *STATUS field for the specified file is returned. The INFDS does not have to be specified for the file. %STATUS starts with a return value of 00000 and is reset to 00000 before any operation with an 'E' extender specified begins. %STATUS is best checked immediately after an operation with the 'E' extender or an error indicator specified, or at the beginning of an INFSR or the *PSSR subroutine.

*For more information, see “File Operations” on page 616, “Result Operations” on page 628, or “Built-in*

## %STR (Get or Store Null-Terminated String)

**Syntax:**
```
%STR(basing pointer{: max-length})(right-hand-side)
```
```
%STR(basing pointer : max-length)(left-hand-side)
```

%STR is used to create or use null-terminated character strings, which are very commonly used in C and C++ applications. The first parameter must be a basing-pointer value. (Any basing pointer expression is valid, such as "%ADDR(DATA)" or "P+1".) The second parameter, if specified, must be a numeric value with zero decimal positions. If not specified, it defaults to the maximum allowed length for defining a character variable. The first parameter must point to storage that is at least as long as the length given by the second parameter. Note: The length operand for %STR always refers to the number of bytes. Error conditions: 1. If the length parameter is less than 1 or greater than the maximum length allowed, an error will occur. 2. If the pointer is not set, an error will occur. 3. If the storage addressed by the pointer is shorter than indicated by the length parameter, either

## %SUBARR (Set/Get Portion of an Array)

**Syntax:**
```
%SUBARR(array:start-index{:number-of-elements})
```

Built-in function %SUBARR returns a section of the specified array starting at start-index. The number of elements returned is specified by the optional number-of-elements parameter. If not specified, the number-of-elements defaults to the remainder of the array. The first parameter of %SUBARR must be an array. That is, a standalone field, data structure, or subfield defined as an array. The first parameter must not be a table name or procedure call. The start-index parameter must be a numeric value with zero decimal positions. A float numeric value is not allowed. The value must be greater than or equal to 1 and less than or equal to the number of elements of the array. The optional number-of-elements parameter must be a numeric value with zero decimal positions. A float numeric value is not allowed. The value must be greater than or equal to 1 and less than or equal to the number of elements remaining in the array after applying the start-index value. Generally, %SUBARR is valid in any expression where an unindexed array is allowed. However, %SUBARR cannot be used in the following places: • as the array argument of built-in function %LOOKUPxx • as a parameter passed by reference %STR Used to Store Null-Terminated String Operations, Expressions, and Functions  751 %SUBARR may be used in the following ways: • On the left-hand side of an assignment using EVAL or EVALR. This changes the specified elements in the specified array. • Within the expression on the right-hand side of an assignment using EVAL or EVALR where the target of the assignment is an array. This uses the values of the specified elements of the array. The array elements are used directly; a temporary copy of the sub-array is not made. • In Extended Factor 2 of the SORTA operation. • In Extended Factor 2 of the RETURN operation. • Passed by VALUE or by read-only reference (CONST keyword) when the corresponding parameter is defined as an array. • As the parameter of the %XFOOT built-in function.

**Examples:**
```rpgle
D a               s             10i 0 dim(5)
D b               s             10i 0 dim(15)
D resultArr       s             10i 0 dim(20)
D sum             s             20i 0
/free
a(1)=9;
a(2)=5;
a(3)=16;
a(4)=13;
a(5)=3;
// Copy part of an array to another array:
resultArr = %subarr(a:4:n);
// this is equivalent to:
//   resultArr(1) = a(4)
//   resultArr(2) = a(5)
//   ...
//   resultArr(n) = a(4 + n - 1)
// Copy part of an array to part of another array:
%subarr(b:3:n) = %subarr(a:m:n);
```

*For more information, see “Array Operations” on page 601 or “Built-in Functions” on page 590.*

## %SUBDT (Extract a Portion of a Date, Time, or Timestamp)

**Syntax:**
```
%SUBDT(value : unit { : digits { : decpos } })
```
```
%SUBDT (Extract a Portion of a Date, Time, or
```

The unit can be *MSECONDS, *SECONDS, *MINUTES, *HOURS, *DAYS, *MONTHS, or *YEARS. You can also use the following abbreviated forms of the units: *MS, *S, *MN, *H, *D, *M, or *Y. %SUBDT extracts a portion of the information in a date, time, or timestamp value. It returns an unsigned numeric value. The first parameter is the date, time, or timestamp value. The second parameter is the portion that you want to extract. The following values are valid: • For a date: *DAYS, *MONTHS, and *YEARS • For a time: *SECONDS, *MINUTES, and *HOURS • For a timestamp: *MSECONDS, *SECONDS, *MINUTES, *HOURS, *DAYS, *MONTHS, and *YEARS Timestamp) Operations, Expressions, and Functions  753 • The third parameter is optional. It represents the number of digits in the returned value. • The fourth parameter is optional. It represents the number of decimal places, or fractional seconds, in the returned value. It can be specified when the first parameter is a timestamp and the second parameter is *SECONDS or *S. For example, if you want the returned value to have 7 decimal places, specify 9 for the digits parameter and 7 for the decpos parameter. For this function, *DAYS always refers to the day of the month not the day of the year (even if you are using a Julian date format). For example, the day portion of February 10 is 10 not 41. This function always returns a 4-digit year, even if the date format has a 2-digit year. date = d'1999-02-17'; time = t'01.23.45'; timestamp = z'1999-02-17-01.23.45.98765';

**Examples:**
```rpgle
num = %subdt(date:*YEARS);
// num = 1999
num = %subdt(time:*MN);
// num = 23
seconds = %subdt(timestamp:*S:5:3);
// seconds = 45.987
```

*For more information, see “Date Operations” on page 612 or “Built-in Functions” on page 590.*

## %SUBST (Get Substring)

**Syntax:**
```
%SUBST(string:start {:length {: *NATURAL | *STDCHARSIZE }})
```

%SUBST returns a portion of argument string. It may also be used as the result of an assignment with the EVAL operation code. The start parameter represents the starting position of the substring. The length parameter represents the length of the substring. If it is not specified, the length is the length of the string parameter less the start value plus one. The string must be character, graphic, or UCS-2data. Starting position and length may be any numeric value or numeric expression with zero decimal positions. The starting position must be greater than zero. The length may be greater than or equal to zero. When the string parameter is varying length, the values of the other parameters are checked against the current length, not the maximum length. The third or fourth parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %SUBST operates in CHARCOUNT NATURAL mode. The start position and length are measured in characters rather than bytes or double bytes. For example, if the base string is a UTF-8 string with the value 'ábç12', a start position of 3 refers to 'ç' because it is the third character. • Specify *STDCHARSIZE to indicate that %SUBST operates in CHARCOUNT STDCHARSIZE mode. In the previous example, with CHARCOUNT STDCHARSIZE mode, a start position of 3 refers to 'b' because it is the third byte. Characters 'á' and 'ç' are 2-byte characters. See “Processing string data by the natural size of each character” on page 295 and “Character Data Type” on page 280. %SUBDT (Extract a Portion of a Date, Time, or Timestamp) Note: %SUBST can also operate in CHARCOUNT NATURAL mode due to the /CHARCOUNT compiler directive or the CHARCOUNT Control keyword. When specified as a parameter for a definition specification keyword, the parameters must be literals or named constants representing literals. When specified on a free-form calculation specification, the parameters may be any expression. %SUBST Used for its Value %SUBST returns a substring from the contents of the specified string. The string may be any character, graphic, or UCS-2 field or expression. Unindexed arrays are allowed for string, start, and length. The substring begins at the specified starting position in the string and continues for the length specified. If length is not specified then the substring continues to the end of the string. For example: The value of  %subst('Hello World': 5+2) is  'World' The value of  %subst('Hello World':5+2:10-7) is 'Wor' The value of  %subst('abcd' + 'efgh':4:3) is 'def' When the starting positing is always 1, the %LEFT built-in function can be used. For information about the CHARCOUNT mode affects %SUBST, see %SUBST with CHARCOUNT NATURAL.

*For more information, see “String Operations” on page 628 or “Built-in Functions” on page 590.*

## %TARGET

**Syntax:**
```
%TARGET (program-or-procedure { : offset } )
```

%TARGET is used as the third operand of the SND-MSG operation. %TARGET does not return a value, and it cannot be specified anywhere other than for the SND-MSG operation. %TARGET specifies the target program or procedure for the message. The first operand can be • *SELF. This is the default for an informational message. The message is sent to the current procedure. • *CALLER. This is the default for an escape, completion, diagnostic, notification, or status message. The message is sent to the caller of the current procedure. • *CTLBDY. This represents the control boundary, which is the first procedure of the group of procedures on the call stack that are in the same activation group as the procedure with the SND-MSG operation. For example, consider the following program stack, where PROC1 called PROC2, which called PROC3, and so on. If PROC5 sends a message with %TARGET(*CTLBDY), the message is sent to PROC3 because it is the first procedure in the contiguous group of procedures that are all in activation group AG1. Procedure PROC1 is also in activation group AG1, but procedure PROC2 is in a different activation group, so procedure PROC1 is not considered when determining the control boundary for PROC5. Procedure Activation group PROC1 AG1 PROC2 AG2 PROC3 AG1 PROC4 AG1 PROC5 AG1 • *EXT. This represents the external message queue. When the message type is *STATUS, the message is displayed at the bottom of the screen. See “Example of showing progress messages at the bottom of the screen with message type *STATUS and %TARGET(*EXT)” on page 946 for an example. *EXT cannot be specified when the message-type operand for SND-MSG is *ESCAPE. • *PGMBDY. This represents the program boundary, which is the first procedure of the group of procedures on the call stack that are in the same program or service program as the procedure with the SND-MSG operation. For example, consider the following program stack, where PROC1 called PROC2, which called PROC3, and so on. If PROC5 sends a message with %TARGET(*PGMBDY), the message is sent to PROC3 because it is the first procedure in the contiguous group of procedures that are all in the same program

## %THIS (Return Class Instance for Native Method)

%THIS %THIS returns an Object value that contains a reference to the class instance on whose behalf the native method is being called. %THIS is valid only in non-static native methods. This built-in gives non-static native methods access to the class instance. A non-static native method works on a specific instance of its class. This object is actually passed as a parameter to the native method by Java, but it does not appear in the prototype or procedure interface for the native method. In a Java method, the object instance is referred to by the Java reserved word this. In an RPG native method, the object instance is referred to by the %THIS built-in function. * Method "vacationDays" is a method in the class 'Employee' D vacationDays    PR            10I 0 EXTPROC(*JAVA D                                           : 'Employee' D                                           : 'vacationDays') * Method "getId" is another method in the class 'Employee' D getId           PR            10I 0 EXTPROC(*JAVA D                                           : 'Employee' D                                           : 'getId') ... * "vacationDays" is an RPG native method.  Since the STATIC keyword * is not used, it is an instance method. P vacationDays    B                   EXPORT D vacationDays    PI            10I 0 D id_num          S             10I 0 * Another Employee method must be called to get the Employee's * id-number.  This method requires an Object of class Employee. * We use %THIS as the Object parameter, to get the id-number for * the object that our native method "vacationDays" is working on. C                   eval      id_num = getId(%THIS) C     id_num        chain     EMPFILE C                   if        %found C                   return    VACDAYS C                   else C                   return    -1 C                   endif P vacationDays    E

## %TIME (Convert to Time)

## %TIMESTAMP (Convert to Timestamp)

%TIMESTAMP{(char-num-expression { : *ISO|*ISO0 : {fractional-seconds}})} %TIMESTAMP{(date-timestamp-expression { : fractional-seconds})} %TIMESTAMP{(*SYS { : fractional-seconds})} %TIMESTAMP{(*UNIQUE)} Using %TIMESTAMP to return the current system timestamp If you do not specify a parameter, or if you specify *SYS or *UNIQUE as the first parameter, %TIMESTAMP returns the current system timestamp, accurate to microsecond precision. If the first parameter is *SYS, the optional second parameter is the number of fractional seconds in the returned timestamp. The number of fractional seconds can be between 0 and 12. It defaults to 6 fractional seconds. If the first parameter is *UNIQUE, %TIMESTAMP returns the current system timestamp, accurate to microsecond precision. The first six digits of the fractional seconds portion of the timestamp are set to the microseconds portion of the timestamp. The remaining six fractional seconds are set to a value which makes the resulting timestamp unique. However, the remaining six fractional seconds do not provide greater precision for the timestamp. Tip: If unique timestamps are used to determine the elapsed time between two unique timestamps, the result should only be calculated to microsecond precision. Using %TIMESTAMP to convert an expression to a timestamp • If the first parameter is a character or numeric expression, the second parameter is the format of the character or numeric data. Regardless of the input format, the output is returned in *ISO format. For character input, you can specify either *ISO (the default) or *ISO0. For more information, see

## %TRIM (Trim Characters at Edges)

**Syntax:**
```
%TRIM(string {: characters to trim {: *NATURAL | *STDCHARSIZE}}})
```

%TRIM with only one parameter returns the given string with any leading and trailing blanks removed. %TRIM with two parameters returns the given string with any leading and trailing characters that are in the characters to trim parameter removed. The string can be character, graphic, or UCS-2 data. If the characters to trim parameter is specified, and it does not have the same type and CCSID as the string parameter, it is converted to the type and CCSID of the string parameter. The second or third parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %TRIM operates in CHARCOUNT NATURAL mode. The number of bytes in each character is considered when locating the characters to trim. 1. For example, if the string parameter is a UTF-8 string with the value 'áçabc', and the characters to trim parameter is a UTF-8 string with the value 'á' only 'á' is considered to be a character to trim. The result is 'çabc'. 2. If the string parameter is a mixed SBCS/DBCS EBCDIC string with the value x'8182830E4CB14DB10F' ('abcDDEE', where 'DD' and 'EE' represents DBCS characters), and the characters to trim parameter has the value x'0E4DB10F' ('EE'), only the last DBCS character is trimmed. The result is x'8182830E4CB10F' ('abcDD'). • Specify *STDCHARSIZE to indicate that %TRIM operates in CHARCOUNT STDCHARSIZE mode. 1. In the first example of the previous paragraph, with CHARCOUNT STDCHARSIZE mode, each byte of the characters to trim parameter is considered to be a separate character. Characters 'á' and 'ç' are

## %TRIML (Trim Leading Characters)

**Syntax:**
```
%TRIML(string {: characters to trim {: *NATURAL | *STDCHARSIZE}}})
```

%TRIML with only one parameter returns the given string with any leading blanks removed. %TRIML with two parameters returns the given string with any leading characters that are in the

## %TRIMR (Trim Trailing Characters)

**Syntax:**
```
%TRIMR(string {: characters to trim {: *NATURAL | *STDCHARSIZE}})
```

%TRIMR with only one parameter returns the given string with any trailing blanks removed. %TRIMR with two parameters returns the given string with any trailing characters that are in the characters to trim parameter removed. The string can be character, graphic, or UCS-2 data. If the characters to trim parameter is specified, and it does not have the same type and CCSID as the string parameter, it is converted to the type and CCSID of the string parameter. The second or third parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. For information about the CHARCOUNT mode affects %TRIMR, see %TRIM with CHARCOUNT NATURAL. When specified as a parameter for a definition specification keyword, the string parameter must be a constant. Note: Specifying %TRIMR with two parameters is not supported for parameters of Definition keywords.

## %UCS2 (Convert to UCS-2 Value)

%UCS2 converts the value of the expression from character, graphic, or UCS-2 and returns a UCS-2 value. The result is varying length if the parameter is varying length, or if the parameter is single-byte character. The second parameter, ccsid, is optional and indicates the CCSID of the resulting expression. The CCSID defaults to the default UCS-2 CCSID of the module as specified by control keyword CCSID(*UCS2). If the parameter is a constant, the conversion will be done at compile time. If the conversion results in substitution characters, a warning message is issued at compile time. At run time, status 00050 is set and no error message is issued.

## %UNS (Convert to Unsigned Format)

**Syntax:**
```
%UNS(numeric or character expression)
```

%UNS converts the value of the expression to unsigned format. Any decimal digits are truncated. %UNS can be used to truncate the decimal positions from a float or decimal value allowing it to be used as an array index. If the parameter is a character expression • See “Rules for converting character values to numeric values using built-in functions” on page 609 for the rules for character expressions for %DEC. • Floating point data, for example '1.2E6', is not allowed. • Floating point data is not allowed. That is, where the numeric value is followed by E and an exponent, for example '1.2E6'. • If invalid numeric data is found, an exception occurs with status code 105

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %UNSH (Convert to Unsigned Format with Half Adjust)

**Syntax:**
```
%UNSH(numeric or character expression)
```
```
%UNSH (Convert to Unsigned Format with Half
```

%UNSH is the same as %UNS except that if the expression is a decimal, float or character value, half adjust is applied to the value of the expression when converting to integer type. No message is issued if half adjust cannot be performed. Adjust) *..1....+....2....+....3....+....4....+....5....+....6....+....7...+....

**Examples:**
```rpgle
D p7              s              7p 3 inz (8236.567)
D s9              s              9s 5 inz (23.73442)
D f8              s              8f   inz (173.789)
D c15a            s             15a   inz (' 12345.6789 +')
D c15b            s             15a   inz (' + 5 , 6 7 ')
D result1         s             15p 5
D result2         s             15p 5
D result3         s             15p 5
D array           s              1a   dim (200)
D a               s              1a
// using numeric parameters
result1 = %uns (p7) + 0.1234; // "result1" is now 8236.12340
result2 = %uns (s9);          // "result2" is now   23.00000
result3 = %unsh (f8);         // "result3" is now  174.00000
// using character parameters
result1 = %uns (c15a);        // "result1" is now 12345.0000
result2 = %unsh (c15b);       // "result2" is now     6.00000
// %UNS and %UNSH can be used as array indexes
a = array (%unsh (f8));
```

*For more information, see “Conversion Operations” on page 608 or “Built-in Functions” on page 590.*

## %UPPER (Convert to Upper Case)

**Syntax:**
```
%UPPER(string {: start { : length { : *NATURAL | *STDCHARSIZE } } })
```

%UPPER returns the string operand converted to upper case. See “%LOWER and %UPPER (Convert to Lower or Upper Case)” on page 708 for detailed information

## %XFOOT (Sum Array Expression Elements)

**Syntax:**
```
%XFOOT(array-expression)
```

%XFOOT results in the sum of all elements of the specified numeric array expression. The precision of the result is the minimum that can hold the result of adding together all array elements, up to a maximum of 63 digits. The number of decimal places in the result is always the same as the decimal places of the array expression. For example, if ARR is an array of 500 elements of precision (17,4), the result of %XFOOT(ARR) is (20,4). For %XFOOT(X) where X has precision (m,n), the following table shows the precision of the result based on the number of elements of X: Elements of X      Precision of %XFOOT(X) 1                  (m,n) 2-10               (m+1,n) 11-100             (m+2,n) 101-1000           (m+3,n) 1001-10000         (m+4,n) 10001-32767        (m+5,n) Normal rules for array expressions apply. For example, if ARR1 has 10 elements and ARR2 has 20 elements, %XFOOT(ARR1+ARR2) results in the sum of the first 10 elements of ARR1+ARR2. This built-in function is similar to the XFOOT operation, except that float arrays are summed like all other types, beginning from index 1 on up.

## %XLATE (Translate)

**Syntax:**
```
%XLATE(from: to: string {: startpos {: *NATURAL | *STDCHARSIZE}})
```

%XLATE translates string according to the values of from, to, and startpos. The first parameter contains a list of characters that should be replaced, and the second parameter contains their replacements. For example, if the string contains the third character in from, every occurrence of that character is replaced with the third character in to. The third parameter is the string to be translated. The fourth parameter is the starting position for translation. By default, translation starts at position 1. The fourth or fifth parameter can be *NATURAL or *STDCHARSIZE to override the current CHARCOUNT mode for the statement. If this parameter is specified, it must be the last parameter. • Specify *NATURAL to indicate that %XLATE operates in CHARCOUNT NATURAL mode. The start position is measured in characters rather than bytes or double bytes. For example, if the string operand is a UTF-8 string with the value 'ábç12', a start position of 3 refers to 'ç' because it is the third character. • Specify *STDCHARSIZE to indicate that %XLATE operates in CHARCOUNT STDCHARSIZE mode. In the previous example, with CHARCOUNT STDCHARSIZE mode, a start position of 3 refers to 'b' because it is the third byte. Characters 'á' and 'ç' are 2-byte characters. See “Processing string data by the natural size of each character” on page 295 and “Character Data Type” on page 280. Note: %XLATE can also operate in CHARCOUNT NATURAL mode due to the /CHARCOUNT compiler directive or the CHARCOUNT Control keyword. If the first parameter is longer than the second parameter, the additional characters in the first parameter are ignored. The first three parameters can be of type character, graphic, or UCS-2. If the first or second parameter does not have the same type or CCSID as the third parameter, it is converted to the type and CCSID of the third parameter. The value returned has the same type and length as string. The fourth parameter is a non-float numeric with zero decimal positions. D up              C                   'ABCDEFGHIJKLMNOPQRSTUVWXYZ' D lo              C                   'abcdefghijklmnopqrstuvwxyz' D string          S             10A   inz('rpg dept')

**Examples:**
```rpgle
string = %XLATE(lo:up:'rpg dept');
// string now contains 'RPG DEPT'
string = %XLATE(up:lo:'RPG DEPT':6);
// string now contains 'RPG Dept'
```

*For more information, see “String Operations” on page 628 or “Built-in Functions” on page 590.*

## %XML

**Syntax:**
```
%XML (xmlDocument {:options})
```

%XML is used as the second operand of the XML-SAX and XML-INTO operation codes to specify the XML document to be parsed, and the options to control how the document is parsed. %XML does not return a

## %YEARS (Number of Years)

**Syntax:**
```
%YEARS(number)
```

%YEARS converts a number into a duration that can be added to a date or timestamp value. %YEARS can only follow the plus or minus sign in an addition or subtraction expression. The value before the plus or minus sign must be a date or timestamp. The result is a date or timestamp value with the appropriate number of years added or subtracted. For a date, the resulting value is in *ISO format. If the date represented by the date or timestamp value is February 29 and the resulting year is not a leap year, February 28 is used instead. Adding or subtracting a number of years to a February 29 date may not be reversible. For example, 2000-02-29 + %YEARS(1) - %YEARS(1) is 2000-02-28. For an example of the %YEARS built-in function, see Figure 228 on page 716. “Unexpected Results” on page 614. Operation Codes This chapter describes, in alphabetical order, each operation code. ACQ (Acquire) Free-Form Syntax ACQ{(E)} device-name workstn-file Code Factor 1 Factor 2 Result Field Indicators ACQ (E) device- name workstn-file _ ER _ The ACQ operation acquires the program device specified by device-name for the WORKSTN file specified by workstn-file. If the device is available, ACQ attaches it to the file. If it is not available or is already attached to the file, an error occurs. To handle ACQ exceptions (file status codes greater than 1000), either the operation code extender 'E' or an error indicator ER can be specified, but not both. If no error indicator or 'E' extender is specified, but the INFSR subroutine is specified, the INFSR receives control when an error/exception occurs. If no indicator, 'E' extender, or INFSR subroutine is specified, the default error/exception handler receives control when an error/exception occurs. For more information on error handling, see “File Exception/ Errors” on page 163. No input or output operation occurs when the ACQ operation is processed. ACQ may be used with a multiple device file or, for error recovery purposes, with a single device file. One program may acquire and have the device available to any called program which shares the file and allow the called program to release the device. See the section on "Multiple-Device Files" in the chapter about using WORKSTN files in the Rational Development Studio for i: ILE RPG Programmer's Guide. ADD (Add) Free-Form Syntax (not allowed - use the + or += operator) Code Factor 1 Factor 2 Result Field Indicators ADD (H) Addend Addend Sum +  Z If factor 1 is specified, the ADD operation adds it to factor 2 and places the sum in the result field. If factor

*For more information, see “Date Operations” on page 612, “Built-in Functions” on page 590, and*
*For more information, see “File Operations” on page 616.*

---

# Quick Reference

| BIF | Title |
|-----|-------|
| %ABS | Absolute Value of Expression |
| %ADDR | Get Address of Variable |
| %ALLOC | Allocate Storage |
| %BITAND | Bitwise AND Operation |
| %BITNOT | Invert Bits |
| %BITOR | Bitwise OR Operation |
| %BITXOR | Bitwise Exclusive-OR Operation |
| %CHAR | Convert to Character Data |
| %CHARCOUNT | Return the Number of Characters |
| %CHECK | Check Characters |
| %CHECKR | Check Reverse |
| %CONCAT | Concatenate with Separator |
| %CONCATARR | Concatenate Array Elements with Separator |
| %DATA | *See details* |
| %DATE | Convert to Date |
| %DAYS | Number of Days |
| %DEC | Convert to Packed Decimal Format |
| %DECH | Convert to Packed Decimal Format with Half Adjust |
| %DECPOS | Get Number of Decimal Positions |
| %DIFF | *See details* |
| %DIV | Return Integer Portion of Quotient |
| %EDITC | Edit Value Using an Editcode |
| %EDITFLT | Convert to Float External Representation |
| %EDITW | Edit Value Using an Editword |
| %ELEM | Get Number of Elements |
| %EOF | Return End or Beginning of File Condition |
| %EQUAL | Return Exact Match Condition |
| %ERROR | Return Error Condition |
| %FIELDS | Fields to update |
| %FLOAT | Convert to Floating Format |
| %FOUND | Return Found Condition |
| %GEN | *See details* |
| %GRAPH | Convert to Graphic Value |
| %HANDLER | *See details* |
| %HIVAL | Highest Value |
| %HOURS | Number of Hours |
| %INT | Convert to Integer Format |
| %INTH | Convert to Integer Format with Half Adjust |
| %KDS | Search Arguments in Data Structure |
| %LEFT | Get Leftmost Characters |
| %LEN | Get or Set Length |
| %LIST | *See details* |
| %LOVAL | Lowest Value |
| %LOWER | Convert to Lower Case |
| %MAX | Maximum Value |
| %MAXARR | Maximum Element in an Array |
| %MIN | Minimum Value |
| %MINARR | Minimum Element in an Array |
| %MINUTES | Number of Minutes |
| %MONTHS | Number of Months |
| %MSECONDS | Number of Microseconds |
| %MSG | *See details* |
| %NULLIND | Query or Set Null Indicator |
| %OCCUR | Set/Get Occurrence of a Data Structure |
| %OMITTED | Return Parameter-Omitted Condition |
| %OPEN | Return File Open Condition |
| %PADDR | Get Procedure Address |
| %PARMNUM | Return Parameter Number |
| %PARMS | Return Number of Parameters |
| %PARSER | *See details* |
| %PASSED | Return Parameter-Passed Condition |
| %PROC | Return Name of Current Procedure |
| %RANGE | *See details* |
| %REALLOC | Reallocate Storage |
| %REM | Return Integer Remainder |
| %REPLACE | Replace Character String |
| %RIGHT | Get Rightmost Characters |
| %SCAN | Scan for Characters |
| %SCANR | Scan Reverse for Characters |
| %SCANRPL | Scan and Replace Characters |
| %SECONDS | Number of Seconds |
| %SHTDN | Shut Down |
| %SIZE | Get Size in Bytes |
| %SPLIT | Split String into Substrings |
| %SQRT | Square Root of Expression |
| %STATUS | Return File or Program Status |
| %STR | Get or Store Null-Terminated String |
| %SUBARR | Set/Get Portion of an Array |
| %SUBDT | Extract a Portion of a Date, Time, or Timestamp |
| %SUBST | Get Substring |
| %TARGET | *See details* |
| %THIS | Return Class Instance for Native Method |
| %TIME | Convert to Time |
| %TIMESTAMP | Convert to Timestamp |
| %TRIM | Trim Characters at Edges |
| %TRIML | Trim Leading Characters |
| %TRIMR | Trim Trailing Characters |
| %UCS2 | Convert to UCS-2 Value |
| %UNS | Convert to Unsigned Format |
| %UNSH | Convert to Unsigned Format with Half Adjust |
| %UPPER | Convert to Upper Case |
| %XFOOT | Sum Array Expression Elements |
| %XLATE | Translate |
| %XML | *See details* |
| %YEARS | Number of Years |