# Query Acceleration Service & Right-Sizing

## Concept
QAS offloads parts of eligible large scans to serverless compute (Enterprise+). Right-size by testing query time vs size: if doubling size halves time, cost is flat and speed improves.

## Commands
```sql
SELECT SYSTEM$ESTIMATE_QUERY_ACCELERATION('<query_id>');
ALTER WAREHOUSE bi_wh SET ENABLE_QUERY_ACCELERATION=TRUE QUERY_ACCELERATION_MAX_SCALE_FACTOR=4;
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_ACCELERATION_ELIGIBLE ORDER BY ELIGIBLE_QUERY_ACCELERATION_TIME DESC LIMIT 20;
```
## Post-checks
```sql
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_ACCELERATION_HISTORY ORDER BY START_TIME DESC LIMIT 20;   -- credits used
```
