# Secure Through SQL

```sql
GRANT `roles/bigquery.dataViewer` ON TABLE my_project.my_dataset.my_table
TO "user:tom@example.com", "user:sara@example.com"
```

```sql
REVOKE `roles/bigquery.admin` ON SCHEMA my_project.my_dataset
FROM "group:example-team@example.com", "serviceAccount:user@test-project.iam.gserviceaccount.com"
```

## Documentation

* [Data control language statements in standard SQL](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-control-language)