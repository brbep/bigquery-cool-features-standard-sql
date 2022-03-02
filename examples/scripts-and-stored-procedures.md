# Scripts and Stored Procedures

## Create Table

```sql
CREATE OR REPLACE TABLE my_project.my_dataset.my_table (customer_id STRING);
```

## Sets a variable, runs an INSERT statement, and displays the result as a formatted text string

```sql
DECLARE id STRING;
SET id = GENERATE_UUID();

INSERT INTO my_project.my_dataset.my_table (customer_id)
   VALUES(id);

SELECT FORMAT("Created customer ID %s", id);
```

## The same script converted into a procedure

```sql
CREATE OR REPLACE PROCEDURE my_dataset.create_customer()
BEGIN
  DECLARE id STRING;
  SET id = GENERATE_UUID();
  INSERT INTO my_project.my_dataset.my_table (customer_id)
    VALUES(id);
  SELECT FORMAT("Created customer %s", id);
END;

CALL my_dataset.create_customer();
```

## Documentation

* [Scripts and stored procedures](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting-concepts)