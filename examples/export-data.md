# Export Data

Exports the results of a query to an external storage location.
Supported formats include: AVRO, CSV, JSON, PARQUET.

```sql
EXPORT DATA OPTIONS(
  uri='gs://my-bucket/my-folder/*.json',
  format='JSON',
  overwrite=true) AS
SELECT * 
FROM `my-project.my-dataset.my-table`
```

## Documentation

* [EXPORT DATA statement](https://cloud.google.com/bigquery/docs/reference/standard-sql/other-statements?hl=en#export_data_statement)