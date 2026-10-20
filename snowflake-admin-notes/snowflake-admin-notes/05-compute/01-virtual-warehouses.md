# Virtual Warehouses

## Concept
Compute clusters for queries/DML/loading. Sizes XS(1 credit/h) → 6XL, doubling per size. Billed per-second, 60 s minimum on each resume. **Scale up** (bigger size) for complex queries; **scale out** (multi-cluster) for concurrency.

## Pre-checks
```sql
SHOW WAREHOUSES;
SELECT WAREHOUSE_NAME, SUM(CREDITS_USED) FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
WHERE START_TIME > DATEADD(day,-30,CURRENT_TIMESTAMP()) GROUP BY 1 ORDER BY 2 DESC;
```
- Plan: one warehouse per workload (ETL, BI, DS, ad-hoc) for cost attribution and isolation.

## Commands
```sql
USE ROLE SYSADMIN;
CREATE WAREHOUSE bi_wh WAREHOUSE_SIZE='XSMALL' AUTO_SUSPEND=60 AUTO_RESUME=TRUE
  INITIALLY_SUSPENDED=TRUE COMMENT='BI workloads';
ALTER WAREHOUSE bi_wh SET WAREHOUSE_SIZE='SMALL';
ALTER WAREHOUSE bi_wh SUSPEND;   ALTER WAREHOUSE bi_wh RESUME;
ALTER WAREHOUSE bi_wh SET STATEMENT_TIMEOUT_IN_SECONDS=1800 STATEMENT_QUEUED_TIMEOUT_IN_SECONDS=300;
ALTER WAREHOUSE bi_wh SET MAX_CONCURRENCY_LEVEL=8;
GRANT USAGE, OPERATE ON WAREHOUSE bi_wh TO ROLE analyst;
DROP WAREHOUSE IF EXISTS old_wh;
```
## Post-checks
```sql
SHOW WAREHOUSES LIKE 'BI_WH';
SELECT * FROM TABLE(INFORMATION_SCHEMA.WAREHOUSE_LOAD_HISTORY(DATE_RANGE_START=>DATEADD(hour,-4,CURRENT_TIMESTAMP()), WAREHOUSE_NAME=>'BI_WH'));
```
## Gotchas
- AUTO_SUSPEND too low loses the local cache; 60 s is a common start, longer for BI.
- Resizing doesn't affect running queries; only new ones.
- Gen2 warehouses and Snowpark-optimized warehouses (`WAREHOUSE_TYPE='SNOWPARK-OPTIMIZED'`) exist for memory-heavy ML work.
