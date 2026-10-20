# User Management

## Concept
User types: `PERSON` (humans), `SERVICE` (key-pair/OAuth only, no password/MFA), `LEGACY_SERVICE`. Each user has a default role, warehouse and namespace.

## Pre-checks
```sql
SHOW USERS LIKE 'john%';
SELECT NAME, DISABLED, LAST_SUCCESS_LOGIN, HAS_MFA FROM SNOWFLAKE.ACCOUNT_USAGE.USERS WHERE DELETED_ON IS NULL;
```
## Commands
```sql
USE ROLE USERADMIN;
CREATE USER john
  PASSWORD = '<TEMP_PASSWORD>' MUST_CHANGE_PASSWORD = TRUE
  DEFAULT_ROLE = analyst DEFAULT_WAREHOUSE = bi_wh DEFAULT_NAMESPACE = sales_db.public
  EMAIL = 'john@example.com' COMMENT = 'Analyst';
GRANT ROLE analyst TO USER john;
CREATE USER svc_etl TYPE = SERVICE DEFAULT_ROLE = etl_role DEFAULT_WAREHOUSE = etl_wh;

ALTER USER john SET DISABLED = TRUE;                   -- disable
ALTER USER john SET PASSWORD = '<NEW>' MUST_CHANGE_PASSWORD = TRUE;
ALTER USER john SET MINS_TO_UNLOCK = 0;                -- unlock locked user
ALTER USER john ABORT ALL QUERIES;
DROP USER john;
DESC USER john;
```
## Post-checks
```sql
SHOW GRANTS TO USER john;
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.LOGIN_HISTORY WHERE USER_NAME='JOHN' ORDER BY EVENT_TIMESTAMP DESC LIMIT 10;
```
## Gotchas
- Dropping a user doesn't drop objects they own; transfer ownership first.
- Review inactive users regularly (e.g. no login in 90 days).
