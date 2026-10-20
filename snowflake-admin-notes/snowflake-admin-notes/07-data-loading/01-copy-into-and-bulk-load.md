# Bulk Loading with COPY INTO

## Concept
`COPY INTO <table> FROM @stage` loads files using a warehouse. Load metadata prevents reloading the same file for 64 days. Unload with `COPY INTO @stage FROM table`.

## Pre-checks
```sql
LIST @sales_db.raw.landing;
SELECT $1,$2,$3 FROM @sales_db.raw.landing (FILE_FORMAT=>'sales_db.raw.csv_ff') LIMIT 10;
COPY INTO sales_db.raw.orders FROM @sales_db.raw.landing VALIDATION_MODE = RETURN_ERRORS;   -- dry run
```
## Commands
```sql
COPY INTO sales_db.raw.orders
  FROM @sales_db.raw.landing/orders/
  FILE_FORMAT = (FORMAT_NAME = 'sales_db.raw.csv_ff')
  ON_ERROR = 'ABORT_STATEMENT'      -- or CONTINUE / SKIP_FILE
  PURGE = FALSE;
-- Force reload (careful: duplicates)
-- COPY INTO ... FORCE = TRUE;

-- Unload
COPY INTO @sales_db.raw.landing/export/ FROM (SELECT * FROM orders) FILE_FORMAT=(TYPE=PARQUET) HEADER=TRUE;
```
## Post-checks
```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(TABLE_NAME=>'ORDERS', START_TIME=>DATEADD(hour,-2,CURRENT_TIMESTAMP())));
SELECT * FROM TABLE(VALIDATE(sales_db.raw.orders, JOB_ID => '_last'));
SELECT COUNT(*) FROM sales_db.raw.orders;
```
## Gotchas
- Aim for 100–250 MB compressed files; avoid thousands of tiny files.
- Load history is per table; recreating the table resets it.
