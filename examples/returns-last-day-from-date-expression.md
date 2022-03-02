# Returns the Last Day from a Date Expression

```sql
--DATE PART: YEAR, QUARTER, MONTH, WEEK, WEEK(SUNDAY, MONDAY etc.), ISOWEEK, ISOYEAR
SELECT LAST_DAY(DATE '2021-02-25', MONTH) AS last_day;
```

## Documentation

* [LAST_DAY](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#last_day)