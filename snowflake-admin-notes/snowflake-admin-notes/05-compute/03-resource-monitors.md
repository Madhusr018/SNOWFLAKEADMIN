# Resource Monitors

## Concept
Track credit usage for the account or specific warehouses and trigger actions: `NOTIFY`, `SUSPEND` (after running queries finish), `SUSPEND_IMMEDIATE` (cancels queries). Only ACCOUNTADMIN creates them by default (can delegate via `MONITOR`/`MODIFY`).

## Pre-checks
```sql
SHOW RESOURCE MONITORS;
-- Set notification email in your user profile; verify email
```
## Commands
```sql
USE ROLE ACCOUNTADMIN;
CREATE RESOURCE MONITOR rm_monthly WITH CREDIT_QUOTA = 500 FREQUENCY = MONTHLY START_TIMESTAMP = IMMEDIATELY
  NOTIFY_USERS = (john)
  TRIGGERS ON 75 PERCENT DO NOTIFY
           ON 90 PERCENT DO NOTIFY
           ON 100 PERCENT DO SUSPEND
           ON 110 PERCENT DO SUSPEND_IMMEDIATE;
ALTER ACCOUNT SET RESOURCE_MONITOR = rm_monthly;        -- account-level
ALTER WAREHOUSE etl_wh SET RESOURCE_MONITOR = rm_etl;   -- warehouse-level
ALTER RESOURCE MONITOR rm_monthly SET CREDIT_QUOTA = 600;
DROP RESOURCE MONITOR rm_old;
```
## Post-checks
```sql
SHOW RESOURCE MONITORS;     -- used_credits, remaining_credits, level
SHOW WAREHOUSES;            -- resource_monitor column
```
## Gotchas
- Monitors only cover warehouse credits, not serverless features or cloud services costs; use **Budgets** for those.
- Suspended warehouses by monitor resume at the next period or after quota is raised.
