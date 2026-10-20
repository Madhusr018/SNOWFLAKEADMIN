# Cost Management

## Concept
Cost = **compute** (warehouse credits, serverless features) + **storage** (TB/month, including Time Travel/Fail-safe) + **cloud services** (beyond 10% daily adjustment) + **data transfer** (egress/cross-region).

## Pre-checks / Reports
```sql
-- Daily credits by service
SELECT usage_date, service_type, SUM(credits_used) FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY
WHERE usage_date > DATEADD(day,-30,CURRENT_DATE()) GROUP BY 1,2 ORDER BY 1 DESC;
-- Warehouse credits
SELECT warehouse_name, SUM(credits_used_compute) c, SUM(credits_used_cloud_services) cs
FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY WHERE start_time > DATEADD(day,-30,CURRENT_TIMESTAMP()) GROUP BY 1 ORDER BY 2 DESC;
-- Storage by table (find big/old/transient candidates)
SELECT table_catalog, table_schema, table_name,
       ROUND(active_bytes/1e9,2) active_gb, ROUND(time_travel_bytes/1e9,2) tt_gb, ROUND(failsafe_bytes/1e9,2) fs_gb
FROM SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS WHERE deleted = FALSE ORDER BY active_bytes DESC LIMIT 50;
-- Currency
SELECT * FROM SNOWFLAKE.ORGANIZATION_USAGE.USAGE_IN_CURRENCY_DAILY ORDER BY usage_date DESC LIMIT 30;
```
## Levers
1. Right-size, short AUTO_SUSPEND, one warehouse per workload.
2. Resource monitors + Budgets.
3. Transient tables for staging; lower retention on churny tables.
4. `STATEMENT_TIMEOUT_IN_SECONDS` to stop runaway queries.
5. Avoid over-clustering/materialized views on volatile data (serverless maintenance cost).
6. Use query tags for chargeback: `ALTER SESSION SET QUERY_TAG='team=finance'`.

## Budgets
```sql
-- Budgets (SNOWFLAKE.CORE.BUDGET class) track total spend incl. serverless; set up in Snowsight (Admin → Cost Management) or SQL per docs.
```
## Post-checks
- Weekly review of top warehouses/users; alert on 2x daily average.
