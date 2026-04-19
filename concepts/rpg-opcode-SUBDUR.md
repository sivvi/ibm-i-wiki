---
title: SUBDUR (Subtract Duration)
section: Operations, Expressions, and Functions
---

# SUBDUR (Subtract Duration)

Free-Form Syntax: not allowed - use the - or -= operators with duration functions such as %YEARS and %MONTHS, or the %DIFF built-in function

## Code

| Factor 1 | Factor 2 | Result Field | Indicators |
|----------|----------|--------------|------------|
| SUBDUR (E) | | | |
| (duration) | Date/Time/ Timestamp | Date/Time/ Timestamp | |
| | Duration: | Duration code | |
| | | | _ ER _ |
| SUBDUR (E) | | | |
| (new date) | Date/Time/ Timestamp | Duration: Duration Code | |
| Date/Time/ Timestamp | | | |
| | | | _ ER _ |

## Description

The SUBDUR operation has been provided to:

- "Subtract a duration" on page 954 to establish a new Date, Time or Timestamp
- "Calculate a duration" on page 954

### Subtract a duration

The SUBDUR operation can be used to subtract a duration specified in factor 2 from a field or constant specified in factor 1 and place the resulting Date, Time or Timestamp in the field specified in the result field.

Factor 1 is optional and may contain a Date, Time or Timestamp field, array, array element, literal or constant. If factor 1 contains a field name, array or array element then its data type must be the same type as the field specified in the result field. If factor 1 is not specified then the duration is subtracted from the field specified in the result field.

Factor 2 is required and contains two subfactors. The first is a numeric field, array or constant with zero decimal positions. If the field is negative then the duration is added to the field. The second subfactor must be a valid duration code indicating the type of duration. The duration code must be consistent with the result field data type. For example, you can subtract a year, month or day duration but not a minute duration from a date field. For list of duration codes and their short forms see "Date Operations" on page 612.

The result field must be a date, time or timestamp data type field, array or array element. If factor 1 is blank, the duration is subtracted from the value in the result field. If the result field is an array, the value in factor 2 is subtracted from each element in the array. If the result field is a time field, the result will always be a valid Time. For example, subtracting 59 minutes from 00:58:59 would give -00:00:01. Since this time is not valid, the compiler adjusts it to 23:59:59.

When subtracting a duration in months from a date, the general rule is that the month portion is decreased by the number of months in the duration, and the day portion is unchanged. The exception to this is when the resulting day portion would exceed the actual number of days in the resulting month. In this case, the resulting day portion is adjusted to the actual month end date. The following examples (which assume a *YMD format) illustrate this point.

- '95/05/30' SUBDUR 1:*MONTHS results in '95/04/30'
  The resulting month portion has been decreased by 1; the day portion is unchanged.
- '95/05/31' SUBDUR 1:*MONTHS results in '95/04/30'
  The resulting month portion has been decreased by 1; the resulting day portion has been adjusted because April has only 30 days.

Similar results occur when subtracting a year duration. For example, subtracting one year from '92/02/29' results in '91/02/28', an adjusted value since the resulting year is not a leap year.

For more information on subtracting month and year durations, see "Unexpected Results" on page 614.

Note: The system places a 15 digit limit on durations. Subtracting a Duration with more than 15 significant digits will cause errors or truncation. These problems can be avoided by limiting the first subfactor in Factor 2 to 15 digits.

### Calculate a duration

The SUBDUR operation can also be used to calculate a duration between:

1. Two dates
2. A date and a timestamp
3. Two times
4. A time and a timestamp
5. Two timestamps

The data types in factor 1 and factor 2 must be compatible types as specified above.

Factor 1 is required and must contain a Date, Time or Timestamp field, subfield, array, array element, constant or literal.

Factor 2 is required and must also contain a Date, Time or Timestamp field, array, array element, literal or constant.

The following duration codes are valid:

- For two dates or a date and a timestamp: *DAYS (*D), *MONTHS (*M), and *YEARS (*Y)
- For two times or a time and a timestamp: *SECONDS (*S), *MINUTES (*MN), and *HOURS (*H)
- For two timestamps: *MSECONDS (*MS), *SECONDS (*S), *MINUTES (*MN), *HOURS (*H), *DAYS (*D), *MONTHS (*M), and *YEARS (*Y)

The result is a number of whole units, with any remainder discarded. For example, 61 minutes is equal to 1 hour and 59 minutes is equal to 0 hours.

The result field consists of two subfactors. The first is the name of a zero decimal numeric field, array or array element in which the result of the operation will be placed. The second subfactor contains a duration code denoting the type of duration. The result field will be negative if the date in factor 1 is earlier than the date in factor 2.

For more information on working with date-time fields see "Date Operations" on page 612.

Note: Calculating a micro-second Duration (*mseconds) can exceed the 15 digit system limit for Durations and cause errors or truncation. This situation will occur when there is more than a 32 year and 9 month difference between the factor 1 and factor 2 entries.

### Possible error situations

1. For subtracting durations:
   - If the value of the Date, Time or Timestamp field in factor 1 is invalid
   - If factor 1 is blank and the value of the result field before the operation is invalid
   - or if the result of the operation is greater than *HIVAL or less than *LOVAL.

2. For calculating durations:
   - If the value of the Date, Time or Timestamp field in factor 1 or factor 2 is invalid
   - or if the result field is not large enough to hold the resulting duration.

In each of these cases an error will be signalled.

If an error is detected, an error will be generated with one of the following program status codes:

- 00103: Result field not large enough to hold result
- 00112: Date, Time or Timestamp value not valid
- 00113: A Date overflow or underflow occurred (that is, the resulting Date is greater than *HIVAL or less than *LOVAL).

The value of the result field remains unchanged. To handle exceptions with program status codes 103, 112 or 113, either the operation code extender 'E' or an error indicator ER can be specified, but not both.

For more information on error handling, see "Program Exception/Errors" on page 179.

### Examples

 CL0N01Factor1+++++++Opcode(E)+Factor2+++++++Result++++++++Len++D+HiLoEq....
 * Determine a LOANDATE which is xx years, yy months, zz days prior to
 * the DUEDATE.
 C     DUEDATE       SUBDUR    XX:*YEARS     LOANDATE
 C                   SUBDUR    YY:*MONTHS    LOANDATE
 C                   SUBDUR    ZZ:*DAYS      LOANDATE
 * Add 30 days to a loan due date
 *
 C                   SUBDUR    -30:*D        LOANDUE
 * Calculate the number of days between LOANDATE and DUEDATE.
 * If DUEDATE is after LOANDATE, the value of NUM_DAYS will be positive.
 C     DUEDATE       SUBDUR    LOANDATE      NUM_DAYS:*D       5 0
 * Determine the number of months between LOANDATE and DUEDATE.
 C     DUEDATE       SUBDUR    LOANDATE      NUM_MONTHS:*M     5 0

Figure 380. SUBDUR Operations
