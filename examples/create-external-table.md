# Create an External Table

External tables let BigQuery query data that is stored outside of BigQuery storage.

```sql
CREATE OR REPLACE EXTERNAL TABLE my_dataset.ext_produce 
(
  item STRING,
  purchases INT64,
  category STRING,
  path STRING
)
OPTIONS (
  format = 'CSV',
  uris = ['gs://my-bucket/file1.csv', 'gs://my-bucket/file2.csv']
);

SELECT * 
FROM `my_dataset.ext_produce`
```

## Documentation

* [CREATE EXTERNAL TABLE statement](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-definition-language?hl=en#create_external_table_statement)