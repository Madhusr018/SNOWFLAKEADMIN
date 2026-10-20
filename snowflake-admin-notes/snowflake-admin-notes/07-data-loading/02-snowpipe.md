# Snowpipe (continuous ingestion)

## Concept
Serverless, event-driven loading. **Auto-ingest** uses cloud notifications (SQS/Event Grid/PubSub). **Snowpipe Streaming** ingests rows directly via SDK/Kafka connector with low latency.

## Pre-checks
- External stage + storage integration working (`LIST @stage`).
- Target table and file format exist; pipe owner has privileges.

## Commands
```sql
CREATE PIPE sales_db.raw.orders_pipe AUTO_INGEST = TRUE AS
  COPY INTO sales_db.raw.orders FROM @sales_db.raw.landing/orders/ FILE_FORMAT=(FORMAT_NAME='sales_db.raw.csv_ff');
SHOW PIPES;       -- notification_channel (SQS ARN) -> configure on S3 bucket event notifications
ALTER PIPE sales_db.raw.orders_pipe REFRESH;           -- load existing files
ALTER PIPE sales_db.raw.orders_pipe SET PIPE_EXECUTION_PAUSED = TRUE;
```
## Post-checks
```sql
SELECT SYSTEM$PIPE_STATUS('sales_db.raw.orders_pipe');      -- executionState, pendingFileCount
SELECT * FROM TABLE(INFORMATION_SCHEMA.COPY_HISTORY(TABLE_NAME=>'ORDERS', START_TIME=>DATEADD(hour,-1,CURRENT_TIMESTAMP())));
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.PIPE_USAGE_HISTORY ORDER BY START_TIME DESC LIMIT 20;
```
## Gotchas
- Files only trigger on events after the pipe is created; use REFRESH for backlog.
- Pipe has no warehouse; cost = serverless compute + per-file overhead.
