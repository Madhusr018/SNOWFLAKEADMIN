# Failover & Failback (Business Critical+)

## Concept
Failover groups replicate databases, users, roles, warehouses, integrations, policies etc. to a secondary account. **Client Redirect** (connection objects) lets apps use one URL that follows the primary.

## Pre-checks
- Both accounts in same org, Business Critical+, replication enabled.
- Failover group includes everything the apps need (roles, users, warehouses, integrations).
```sql
SHOW FAILOVER GROUPS;
SELECT * FROM TABLE(INFORMATION_SCHEMA.REPLICATION_GROUP_REFRESH_HISTORY('FG_PROD')) ORDER BY START_TIME DESC LIMIT 5;
```
## Commands
```sql
-- Primary
CREATE FAILOVER GROUP fg_prod OBJECT_TYPES = USERS, ROLES, WAREHOUSES, RESOURCE MONITORS, DATABASES, INTEGRATIONS
  ALLOWED_DATABASES = sales_db ALLOWED_ACCOUNTS = myorg.dr_acct
  ALLOWED_INTEGRATION_TYPES = SECURITY INTEGRATIONS, API INTEGRATIONS
  REPLICATION_SCHEDULE = '10 MINUTE';
-- DR account
CREATE FAILOVER GROUP fg_prod AS REPLICA OF myorg.prod_acct.fg_prod;
ALTER FAILOVER GROUP fg_prod REFRESH;
-- Client redirect
CREATE CONNECTION prod_conn;  ALTER CONNECTION prod_conn ENABLE FAILOVER TO ACCOUNTS myorg.dr_acct;

-- FAILOVER: run on DR account
ALTER FAILOVER GROUP fg_prod PRIMARY;
ALTER CONNECTION prod_conn PRIMARY;
-- FAILBACK: after original recovers, refresh it as secondary, then promote it back the same way
```
## Post-checks
```sql
SHOW FAILOVER GROUPS;       -- is_primary = true on DR
SELECT COUNT(*) FROM sales_db.raw.orders;
-- resume tasks/pipes as needed, test app connectivity, check scheduled jobs
```
## Gotchas
- Data lag = RPO (depends on schedule). Document RTO/RPO and **test failover** at least yearly.
- Tasks/pipes may need resume; verify external-stage and notification setup in DR region.
