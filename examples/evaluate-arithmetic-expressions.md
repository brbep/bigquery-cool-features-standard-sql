# Evaluation of dynamic arithmetic expressions with column name in place of value

```sql
CREATE TEMP FUNCTION fn_evaluate_expression(expr STRING) RETURNS FLOAT64 LANGUAGE js AS 
R"""
  return eval(expr);
""";

CREATE TEMP FUNCTION fn_render_values_in_expression(json_values STRING, expression STRING) AS (
(
    /*
      json_values = columns/values converted to JSON format
      expression = expression with the column name to be mapped
    */    
    
    /* 
      Transform JSON into TABULAR format
      StackOverflow code inspiration: https://stackoverflow.com/questions/65048929/bigquery-extract-keys-from-json-object-convert-json-from-object-to-key-value-a
    */
    WITH kv_table AS (
      SELECT
        ARRAY(
            SELECT AS STRUCT 
              LOWER(TRIM(SPLIT(kv, ':')[OFFSET(0)])) as key, 
              TRIM(SPLIT(kv, ':')[OFFSET(1)]) as value
            FROM t.kv AS kv
          ) AS key_value  
      FROM UNNEST([STRUCT(SPLIT(TRANSLATE(json_values, '{}"', '')) AS kv)]) AS t
    ),
    
    kv_table_col_values AS (
      SELECT
        kv.key,
        IF(LOWER(kv.value) = 'null', NULL, kv.value) AS value
      FROM kv_table
      CROSS JOIN UNNEST(key_value) AS kv
    ),

    /* Transform EXPRESSION into TABULAR format */
    expression_table AS (
      SELECT 
        TRIM(col) AS col
      FROM UNNEST(SPLIT(REGEXP_REPLACE(REPLACE(expression, ',', ' '), r'\b', ','), ',')) AS col
    )
    
    /* Render VALUES in EXPRESSION */
    SELECT
      REPLACE(STRING_AGG(IF(c.key IS NULL, m.col, c.value)), ',', '')
    FROM expression_table AS m
    LEFT JOIN kv_table_col_values as c
      ON LOWER(m.col) = c.key
    WHERE
      NULLIF(TRIM(m.col), '') IS NOT NULL
)
);

WITH base_table AS (
    SELECT
      id,
      CAST(RAND()*100 AS INT64) AS a, 
      CAST(RAND()*10 AS INT64) AS b, 
      CAST(RAND()*10 AS INT64) AS c,
      CASE 
        WHEN MOD(id, 6) = 0 THEN 'a+b' 
        WHEN MOD(id, 5) = 0 THEN 'a+b/c' 
        WHEN MOD(id, 4) = 0 THEN 'c*b' 
        WHEN MOD(id, 3) = 0 THEN 'a*b-c/15' 
        WHEN MOD(id, 2) = 0 THEN 'a*(b-c)/15' 
        ELSE 'a+b'
        END AS eqn
    FROM UNNEST(GENERATE_ARRAY(1, 1000000)) AS id --Increase the range to scale
)

SELECT 
  * ,
  fn_render_values_in_expression(TO_JSON_STRING(t), eqn) AS eqn_rendered_values,
  fn_evaluate_expression(fn_render_values_in_expression(TO_JSON_STRING(t), eqn)) AS expected_result
FROM base_table AS t
```

## Documentation

* [Standard SQL](https://cloud.google.com/bigquery/docs/reference/standard-sql/introduction)
* [Standard SQL user-defined functions](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions)
* [TO_JSON_STRING function](https://cloud.google.com/bigquery/docs/reference/standard-sql/json_functions#to_json_string)
* [SPLIT function](https://cloud.google.com/bigquery/docs/reference/standard-sql/string_functions#split)
* [GENERATE_ARRAY function](https://cloud.google.com/bigquery/docs/reference/standard-sql/array_functions#generate_array)