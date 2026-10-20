# Snowflake Architecture

## Concept
Three independent layers:
1. **Storage layer** – data stored as compressed, columnar micro-partitions (50–500 MB uncompressed) in cloud object storage (S3/Blob/GCS). Managed fully by Snowflake.
2. **Compute layer** – *virtual warehouses* (MPP clusters). Independent of storage; scale up/out/pause separately. Each warehouse has its own local cache.
3. **Cloud Services layer** – authentication, metadata, query parsing/optimization, access control, transaction management, result cache.

Key ideas: separation of storage & compute, zero-copy cloning, Time Travel, pay-per-second compute (60 s minimum on resume), storage billed per TB/month.

## Pre-checks
- Know your cloud (AWS/Azure/GCP) and region – cannot change for an existing account (migrate via replication).

## Commands
```sql
SELECT CURRENT_ACCOUNT(), CURRENT_REGION(), CURRENT_VERSION();
SELECT CURRENT_ORGANIZATION_NAME(), CURRENT_ACCOUNT_NAME();
SHOW REGIONS;
```

## Post-checks
- Confirm region/cloud matches where your source data and users are (latency and egress cost).

## Gotchas
- Cloud services cost is free up to 10% of daily compute credits, charged beyond that.
- Result cache lives 24 h (extended up to 31 days if reused) and needs no warehouse.
