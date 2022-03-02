# Debugging Statements

Evaluates expression. If expression evaluates to `TRUE`, the statement returns successfully without any result. If expression evaluates to `FALSE` or `NULL`, the statement generates an error. If AS description is present, description will appear in the error message.

```sql
ASSERT
(SELECT COUNT(*) FROM UNNEST([1, 2, 3, 4, 5, 6])) > 5
AS 'Table must contain more than 5 rows.';

ASSERT EXISTS(
  SELECT X
  FROM UNNEST([7877, 7879, 7883, 7901, 7907]) X
  WHERE X = 7919
) AS 'Column X must contain the value 7919';
```

## Documentation

* [ASSERT](https://cloud.google.com/bigquery/docs/reference/standard-sql/debugging-statements)