# Generate a Row ID Based on Column Values 

```sql
WITH produce AS
 (SELECT 'kale' as item, 23 as purchases, 'vegetable' as category
  UNION ALL SELECT 'kale', 23, 'vegetable' 
  UNION ALL SELECT 'kale', 23, 'vegetable' 
  UNION ALL SELECT 'orange', 2, 'fruit'
  UNION ALL SELECT 'cabbage', 9, 'vegetable'
  UNION ALL SELECT 'apple', 8, 'fruit'
  UNION ALL SELECT 'leek', 2, 'vegetable'
  UNION ALL SELECT 'lettuce', 10, 'vegetable')
 
SELECT 
  *,
  TO_JSON_STRING(t) AS json_from_columns,
  FARM_FINGERPRINT(TO_JSON_STRING(t)) AS rowid_from_columns
FROM produce AS t
```

## Documentation

* [TO_JSON_STRING](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#to_json_string)
* [FARM_FINGERPRINT](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#farm_fingerprint)