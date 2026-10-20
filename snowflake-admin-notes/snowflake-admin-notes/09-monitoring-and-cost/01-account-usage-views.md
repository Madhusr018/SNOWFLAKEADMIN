# Monitoring Views

## Concept
- `SNOWFLAKE.ACCOUNT_USAGE` – account-wide, 365-day history, **latency 45 min–3 h**, includes dropped objects.
- `<DB>.INFORMATION_SCHEMA` – real-time, per-database, shorter history (7 days–6 months), table functions.
- `SNOWFLAKE.ORGANIZATION_USAGE` – org level.
Access: `GRANT IMPORTED PRIVILEGES ON DATABASE SNOWFLAKE TO ROLE monitor_role;` or granular `SNOWFLAKE.USAGE_VIEWER`/`GOVERNANCE_VIEWER` database roles.

## Commands
```sql
GRANT DATABASE ROLE SNOWFLAKE.USAGE_VIEWER TO ROLE monitor_role;
-- Top slow queries
SELECT query_id, user_name, warehouse_name, total_elapsed_time/1000 sec, bytes_scanned, query_text
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE start_time > DATEADD(day,-1,CURRENT_TIMESTAMP())
ORDER BY total_elapsed_time DESC LIMIT 20;
-- Queued / failed
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE execution_status='FAIL' AND start_time > DATEADD(day,-1,CURRENT_TIMESTAMP());
-- Running now
SELECT * FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY_BY_WAREHOUSE(WAREHOUSE_NAME=>'BI_WH')) WHERE execution_status='RUNNING';
SELECT SYSTEM$CANCEL_QUERY('<query_id>');
```
## Key views
QUERY_HISTORY, LOGIN_HISTORY, WAREHOUSE_METERING_HISTORY, METERING_DAILY_HISTORY, STORAGE_USAGE, TABLE_STORAGE_METRICS, COPY_HISTORY, TASK_HISTORY, PIPE_USAGE_HISTORY, GRANTS_TO_ROLES/USERS, ACCESS_HISTORY, SERVERLESS_TASK_HISTORY, AUTOMATIC_CLUSTERING_HISTORY.
