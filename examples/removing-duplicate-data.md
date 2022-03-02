# Removing Duplicate Data

```sql
# Data Source
WITH produce AS
 (SELECT 'kale' as item, 23 as purchases, 'vegetable' as category, CURRENT_TIMESTAMP()-MAKE_INTERVAL(minute => 2) AS updated_timestamp
  UNION ALL SELECT 'kale' as item, 23 as purchases, 'vegetable' as category, CURRENT_TIMESTAMP()-MAKE_INTERVAL(second => 63) AS updated_timestamp
  UNION ALL SELECT 'kale' as item, 23 as purchases, 'vegetable' as category, CURRENT_TIMESTAMP()-MAKE_INTERVAL(second => 75) AS updated_timestamp
  UNION ALL SELECT 'orange', 2, 'fruit', CURRENT_TIMESTAMP()-MAKE_INTERVAL(second => 72) AS updated_timestamp
  UNION ALL SELECT 'cabbage', 9, 'vegetable', CURRENT_TIMESTAMP()-MAKE_INTERVAL(hour => 7) AS updated_timestamp
  UNION ALL SELECT 'apple', 8, 'fruit', CURRENT_TIMESTAMP()-MAKE_INTERVAL(second => 33) AS updated_timestamp
  UNION ALL SELECT 'leek', 2, 'vegetable', CURRENT_TIMESTAMP()-MAKE_INTERVAL(second => 157) AS updated_timestamp
  UNION ALL SELECT 'lettuce', 10, 'vegetable', CURRENT_TIMESTAMP()-MAKE_INTERVAL(hour => 1000) AS updated_timestamp),

# Deduplication Options

# By ROW_NUMBER
dedup_by_rownumber AS (
  SELECT * EXCEPT(rnumber)
  FROM (
    SELECT
      *,
      ROW_NUMBER() OVER (PARTITION BY item ORDER BY updated_timestamp DESC) AS rnumber
    FROM produce )
  WHERE rnumber = 1
),

# By QUALIFY
dedup_by_qualify AS (
  SELECT *
  FROM produce
  WHERE true
  QUALIFY ROW_NUMBER() OVER (PARTITION BY item ORDER BY updated_timestamp DESC) = 1
),

# By ARRAY_AGG
dedup_by_arrayagg AS (
  SELECT event.*
  FROM (
    SELECT 
      ARRAY_AGG(pd ORDER BY pd.updated_timestamp DESC LIMIT 1)[OFFSET(0)] AS event
    FROM produce AS pd
    GROUP BY pd.item)
)

SELECT *, 'ROW_NUMBER' AS dedup_by FROM dedup_by_rownumber
UNION ALL SELECT *, 'QUALIFY' FROM dedup_by_qualify
UNION ALL SELECT *, 'ARRAY_AGG' FROM dedup_by_arrayagg
```

## TIPs

* The `QUALIFY` clause filters the results of analytic functions.

* **First or last record**: When trying to calculate the first or last record in a subset of your data, using the `ROW_NUMBER()` function can fail with Resources Exceeded errors if there are too many elements to `ORDER BY` in a single partition. Instead, try using `ARRAY_AGG()` - which runs more efficiently because the `ORDER BY` is allowed to drop everything except the top record on each `GROUP BY`.

## Documentation

* [MAKE_INTERVAL](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#make_interval)
* [ROW_NUMBER](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#row_number)
* [QUALIFY](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#qualify_clause)
* [ARRAY_AGG](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#array_agg)
