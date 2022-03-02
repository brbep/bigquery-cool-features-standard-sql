# Dynamic SQL

Executes a dynamic SQL statement on the fly.

```sql
DECLARE y INT64;

-- y = 1 * (3 + 2) = 5
EXECUTE IMMEDIATE "SELECT ? * (? + 2)" USING 1, 3;

-- y = 1 * (3 + 2) + 3 = 8
EXECUTE IMMEDIATE "SELECT @a * (@b + 2) + @b" INTO y USING 1 as a, 3 as b;

SELECT y;
```

## Documentation

* [EXECUTE IMMEDIATE](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting?hl=en#execute_immediate)