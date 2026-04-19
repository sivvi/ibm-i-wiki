---
title: EXTRCT (Extract Date/Time/Timestamp)
section: 842
---

# EXTRCT (Extract Date/Time/Timestamp)

## Free-Form Syntax

```
(not allowed - use the %SUBDT built-in function)
```

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| EXTRCT (E) | Date/Time: Duration Code | Target | |
| | | | ER |

## Description

The EXTRCT operation code will return one of:
- The year, month or day part of a date or timestamp field
- The hours, minutes or seconds part of a time or timestamp field
- The microseconds part of the timestamp field

The EXTRCT operation only supports extracting microseconds (6 fractional seconds) from a timestamp field. If you want to extract a different number of fractional seconds, use the %SUBDT built-in function. to the field specified in the result field.

The Date, Time or Timestamp from which the information is required, is specified in factor 2, followed by the duration code. The entry specified in factor 2 can be a field, subfield, table element, or array element. The duration code must be consistent with the Data type of factor 2. See "Date Operations" on page 612 for valid duration codes.

Factor 1 must be blank.

The result field can be any numeric or character field, subfield, array/table element. The result field is cleared before the extracted data is assigned. For a character result field, the data is put left adjusted into the result field.

**Note:** When using the EXTRCT operation with a Julian Date (format *JUL), specifying a duration code of *D will return the day of the month, specifying *M will return the month of the year. If you require the day and month to be in the 3-digit format, you can use a basing pointer to obtain it. See Figure 95 on page 317 for an example of obtaining the Julian format.

To handle EXTRCT exceptions (program status code 112), either the operation code extender 'E' or an error indicator ER can be specified, but not both. For more information on error handling, see "Program Exception/Errors" on page 179.

For more information, see "Date Operations" on page 612.
