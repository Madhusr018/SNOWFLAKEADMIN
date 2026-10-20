# Multi-Cluster Warehouses (Enterprise+)

## Concept
Automatically add clusters (up to MAX) as concurrency grows. Modes: **Auto-scale** (MIN < MAX) or **Maximized** (MIN = MAX).
Scaling policy: `STANDARD` (favors performance, starts clusters quickly) or `ECONOMY` (favors cost, waits to fill clusters).

## Commands
```sql
CREATE WAREHOUSE bi_wh_mc WAREHOUSE_SIZE='MEDIUM' MIN_CLUSTER_COUNT=1 MAX_CLUSTER_COUNT=4
  SCALING_POLICY='STANDARD' AUTO_SUSPEND=120 AUTO_RESUME=TRUE;
ALTER WAREHOUSE bi_wh_mc SET MAX_CLUSTER_COUNT=6;
```
## Post-checks
```sql
SHOW WAREHOUSES LIKE 'BI_WH_MC';     -- started_clusters, min/max
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_EVENTS_HISTORY WHERE WAREHOUSE_NAME='BI_WH_MC' ORDER BY TIMESTAMP DESC LIMIT 50;
```
## Gotchas
- Multi-cluster helps **concurrency/queuing**, not a single slow query.
- Max credits/hour = size credits × MAX_CLUSTER_COUNT. Cap with a resource monitor.
