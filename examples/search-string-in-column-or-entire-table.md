# Search for a String in a Column or an Entire Table

Performs a normalized, case-insensitive search to see if a value exists in an expression. Returns `TRUE` if the value exists, otherwise returns `FALSE`.

```sql
DECLARE search_value STRING DEFAULT 'toast';

WITH recipes AS
 (SELECT 'Blueberry pancakes' as breakfast, 'Egg salad sandwich' as lunch, 'Potato dumplings' as dinner UNION ALL
  SELECT 'Potato pancakes', 'Toasted cheese sandwich', 'Beef stroganoff' UNION ALL
  SELECT 'Ham scramble', 'Steak avocado salad', 'Tomato pasta' UNION ALL
  SELECT 'Avocado toast', 'Tomato soup', 'Blueberry salmon' UNION ALL
  SELECT 'Corned beef hash', 'Lentil potato soup', 'Glazed ham')
  
SELECT 
  *,
  CONTAINS_SUBSTR(breakfast, search_value) AS breakfast_result
FROM recipes 
WHERE CONTAINS_SUBSTR(recipes, search_value);
```

## Documentation

*  [CONTAINS_SUBSTR](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#contains_substr)