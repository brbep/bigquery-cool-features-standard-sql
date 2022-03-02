# Access Historical Data Using Time Travel

Using `time travel` you can access the data from any point within the last seven days. This feature is offered and managed by Google and you don't need to do anything to get it, just run a sql query to access the historical data.

```sql
SELECT *
FROM `my_project.my_dataset.my_table`
FOR SYSTEM_TIME AS OF TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 SECOND);
```

## Documentation

* [Time Travel](https://cloud.google.com/bigquery/docs/time-travel)