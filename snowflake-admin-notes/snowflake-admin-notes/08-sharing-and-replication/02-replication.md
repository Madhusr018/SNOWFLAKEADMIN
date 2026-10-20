# Database & Account Replication

## Concept
Copies objects from a primary to secondary accounts (across regions/clouds) for sharing, DR and migration. Use **replication groups** (replicate, read-only secondary) or **failover groups** (also support failover; Business Critical+). Refreshes are incremental; schedule them.

## Pre-checks
- Source and target accounts in same org; ORGADMIN enables replication for accounts:
```sql
USE ROLE ORGADMIN;
SELECT SYSTEM$GLOBAL_ACCOUNT_SET_PARAMETER('myorg.acct1','ENABLE_ACCOUNT_DATABASE_REPLICATION','true');
SHOW REPLICATION ACCOUNTS;
```
- Replication of external stages/integrations/network policies etc. requires them to be included in group object types.

## Commands
```sql
-- On source
CREATE REPLICATION GROUP rg_sales OBJECT_TYPES = DATABASES ALLOWED_DATABASES = sales_db
  ALLOWED_ACCOUNTS = myorg.acct2 REPLICATION_SCHEDULE = '60 MINUTE';
-- On target
CREATE REPLICATION GROUP rg_sales AS REPLICA OF myorg.acct1.rg_sales;
ALTER REPLICATION GROUP rg_sales REFRESH;
```
## Post-checks
```sql
SHOW REPLICATION GROUPS;
SELECT * FROM TABLE(INFORMATION_SCHEMA.REPLICATION_GROUP_REFRESH_HISTORY('RG_SALES'));
-- compare row counts / latest timestamps on secondary
```
## Gotchas
- Replication has data-transfer and compute costs; monitor `REPLICATION_GROUP_USAGE_HISTORY`.
- Secondary objects are read-only until promoted.
