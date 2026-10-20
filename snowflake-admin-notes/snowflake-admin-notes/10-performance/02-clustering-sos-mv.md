# Clustering, Search Optimization, Materialized Views

## Concept
- **Clustering keys**: co-locate data for pruning on very large tables (multi-TB). Automatic Clustering is serverless, so it costs credits.
- **Search Optimization Service (Enterprise+)**: speeds selective point-lookups.
- **Materialized views (Enterprise+)**: precomputed, auto-maintained results for a single table query.

## Pre-checks
```sql
SELECT SYSTEM$CLUSTERING_INFORMATION('sales_db.raw.orders','(created_at)');   -- avg_depth, overlaps
SELECT SYSTEM$ESTIMATE_SEARCH_OPTIMIZATION_COSTS('sales_db.raw.orders');
```
## Commands
```sql
ALTER TABLE sales_db.raw.orders CLUSTER BY (created_at, region);
ALTER TABLE sales_db.raw.orders SUSPEND RECLUSTER;   ALTER TABLE sales_db.raw.orders RESUME RECLUSTER;
ALTER TABLE sales_db.raw.orders DROP CLUSTERING KEY;

ALTER TABLE sales_db.raw.orders ADD SEARCH OPTIMIZATION ON EQUALITY(id);
ALTER TABLE sales_db.raw.orders DROP SEARCH OPTIMIZATION;

CREATE MATERIALIZED VIEW sales_db.curated.mv_sales AS SELECT region, SUM(amount) amt FROM sales_db.raw.orders GROUP BY region;
```
## Post-checks
```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.AUTOMATIC_CLUSTERING_HISTORY(TABLE_NAME=>'ORDERS')); -- credit cost
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.SEARCH_OPTIMIZATION_HISTORY ORDER BY START_TIME DESC LIMIT 10;
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.MATERIALIZED_VIEW_REFRESH_HISTORY ORDER BY START_TIME DESC LIMIT 10;
```
## Gotchas
- Cluster only tables with frequent filtering on the key and low write churn; high-cardinality keys cost more.
