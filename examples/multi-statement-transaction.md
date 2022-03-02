# Multi-Statement Transactions

```sql
CREATE OR REPLACE TABLE my_dataset.inventory
(
 product string,
 quantity int64,
 supply_constrained bool
);

CREATE OR REPLACE TABLE my_dataset.new_arrivals
(
 product string,
 quantity int64,
 warehouse string
);

INSERT my_dataset.inventory (product, quantity)
VALUES('top load washer', 10),
     ('front load washer', 20),
     ('dryer', 30),
     ('refrigerator', 10),
     ('microwave', 20),
     ('dishwasher', 30);

INSERT my_dataset.new_arrivals (product, quantity, warehouse)
VALUES('top load washer', 100, 'warehouse #1'),
     ('dryer', 200, 'warehouse #2'),
     ('oven', 300, 'warehouse #1');

BEGIN TRANSACTION;

-- Create a temporary table that holds new arrivals from 'warehouse #1'.
CREATE TEMP TABLE tmp
  AS SELECT * FROM my_dataset.new_arrivals WHERE warehouse = 'warehouse #1';

-- Delete the matching records from the NewArravals table.
DELETE my_dataset.new_arrivals WHERE warehouse = 'warehouse #1';

-- Merge the records from the temporary table into the inventory table.
MERGE my_dataset.inventory AS I
USING tmp AS T
ON I.product = T.product
WHEN NOT MATCHED THEN
 INSERT(product, quantity, supply_constrained)
 VALUES(product, quantity, false)
WHEN MATCHED THEN
 UPDATE SET quantity = I.quantity + T.quantity;

-- Drop the temporary table and commit the transaction.
DROP TABLE tmp;

COMMIT TRANSACTION;
```

## Documentation

* [Multi-statement transactions](https://cloud.google.com/bigquery/docs/reference/standard-sql/transactions?hl=en)