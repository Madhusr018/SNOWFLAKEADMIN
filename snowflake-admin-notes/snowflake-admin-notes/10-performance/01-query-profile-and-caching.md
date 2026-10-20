# Query Profile & Caching

## Concept
Three caches: **Result cache** (24 h, cloud services, free), **Local disk (warehouse) cache** (lost on suspend), **Metadata cache** (counts/min/max).
Query Profile (Snowsight → Query History → profile) shows operator tree, bytes scanned, partitions scanned/total, spilling.

## Pre-checks
```sql
SELECT query_id, partitions_scanned, partitions_total, bytes_spilled_to_local_storage, bytes_spilled_to_remote_storage, queued_overload_time
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE query_id='<id>';
```
## Red flags & fixes
| Symptom | Fix |
|---------|-----|
| Partitions scanned ≈ total | Add filter on clustered column; cluster key; search optimization |
| Remote spilling | Bigger warehouse; reduce data/joins |
| Queuing (`queued_overload_time`) | Multi-cluster; split workloads |
| Exploding join | Check join keys/duplicates |
| `SELECT *` on wide tables | Select required columns only |

## Commands
```sql
ALTER SESSION SET USE_CACHED_RESULT = FALSE;   -- benchmark without result cache
```
## Post-checks
- Re-run and compare elapsed time and bytes scanned.
