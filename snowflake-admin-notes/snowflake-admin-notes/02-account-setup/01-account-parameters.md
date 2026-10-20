# Account / Session / Object Parameters

## Concept
Parameters control behavior at three levels: **ACCOUNT → USER → SESSION** (and object-level for warehouses/databases/etc.). Lower level overrides higher level.

## Pre-checks
```sql
SHOW PARAMETERS IN ACCOUNT;
SHOW PARAMETERS LIKE 'STATEMENT%' IN ACCOUNT;
```
## Commands
```sql
USE ROLE ACCOUNTADMIN;
ALTER ACCOUNT SET TIMEZONE = 'Asia/Kolkata';
ALTER ACCOUNT SET STATEMENT_TIMEOUT_IN_SECONDS = 3600;          -- kill runaway queries
ALTER ACCOUNT SET STATEMENT_QUEUED_TIMEOUT_IN_SECONDS = 600;
ALTER ACCOUNT SET DATA_RETENTION_TIME_IN_DAYS = 7;              -- >1 needs Enterprise+
ALTER ACCOUNT SET MIN_DATA_RETENTION_TIME_IN_DAYS = 3;          -- floor that objects can't go below
ALTER ACCOUNT SET REQUIRE_STORAGE_INTEGRATION_FOR_STAGE_CREATION = TRUE;
ALTER ACCOUNT SET REQUIRE_STORAGE_INTEGRATION_FOR_STAGE_OPERATION = TRUE;
ALTER ACCOUNT SET PREVENT_UNLOAD_TO_INLINE_URL = TRUE;
ALTER ACCOUNT SET CLIENT_SESSION_KEEP_ALIVE = TRUE;
-- Session / user level
ALTER USER john SET STATEMENT_TIMEOUT_IN_SECONDS = 600;
ALTER SESSION SET QUERY_TAG = 'etl_nightly';
```
## Post-checks
```sql
SHOW PARAMETERS LIKE 'TIMEZONE' IN ACCOUNT;   -- check "level" column = ACCOUNT
```
## Gotchas
- Setting a warehouse-level `STATEMENT_TIMEOUT_IN_SECONDS` overrides the account value when lower.
