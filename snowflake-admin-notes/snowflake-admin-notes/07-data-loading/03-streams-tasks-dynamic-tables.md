# Streams, Tasks, Dynamic Tables

## Concept
- **Stream**: change data capture (CDC) on a table/view (`METADATA$ACTION`, `ISUPDATE`, `ROW_ID`).
- **Task**: scheduled SQL/procedure; supports DAGs (`AFTER`) and serverless mode.
- **Dynamic table**: declare the query + `TARGET_LAG`; Snowflake refreshes incrementally.

## Pre-checks
```sql
GRANT EXECUTE TASK ON ACCOUNT TO ROLE etl_role;      -- as ACCOUNTADMIN
GRANT EXECUTE MANAGED TASK ON ACCOUNT TO ROLE etl_role;   -- serverless tasks
```
## Commands
```sql
CREATE STREAM orders_stream ON TABLE sales_db.raw.orders;
CREATE TASK merge_orders WAREHOUSE = etl_wh SCHEDULE = '5 MINUTE'
  WHEN SYSTEM$STREAM_HAS_DATA('orders_stream')
AS MERGE INTO sales_db.curated.orders t USING orders_stream s ON t.id = s.id
   WHEN MATCHED AND s.METADATA$ACTION='INSERT' THEN UPDATE SET t.amount = s.amount
   WHEN NOT MATCHED THEN INSERT (id, amount) VALUES (s.id, s.amount);
ALTER TASK merge_orders RESUME;     -- tasks are created suspended
ALTER TASK merge_orders SUSPEND;
EXECUTE TASK merge_orders;          -- manual run

CREATE DYNAMIC TABLE sales_db.curated.daily_sales TARGET_LAG = '15 minutes' WAREHOUSE = etl_wh
AS SELECT DATE(created_at) d, SUM(amount) total FROM sales_db.raw.orders GROUP BY 1;
```
## Post-checks
```sql
SHOW STREAMS;  SHOW TASKS;
SELECT * FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY(TASK_NAME=>'MERGE_ORDERS')) ORDER BY SCHEDULED_TIME DESC LIMIT 10;
SELECT SYSTEM$STREAM_HAS_DATA('orders_stream');
SHOW DYNAMIC TABLES;
```
## Gotchas
- A stream offset advances only when consumed in a DML transaction; stale streams (beyond retention) become unusable.
- Suspend root task before altering a DAG's children.
