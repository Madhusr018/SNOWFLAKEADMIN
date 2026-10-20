# Auditing: Access History, Login History, Query History

## Concept
Know who accessed what. `ACCESS_HISTORY` (Enterprise+) records columns read/written. Combine with `LOGIN_HISTORY`, `QUERY_HISTORY`, `GRANTS_TO_*`.

## Commands
```sql
-- Failed logins
SELECT EVENT_TIMESTAMP, USER_NAME, CLIENT_IP, ERROR_MESSAGE
FROM SNOWFLAKE.ACCOUNT_USAGE.LOGIN_HISTORY WHERE IS_SUCCESS='NO' ORDER BY EVENT_TIMESTAMP DESC LIMIT 100;
-- Who read a sensitive table in last 7 days
SELECT query_start_time, user_name, direct_objects_accessed
FROM SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY
WHERE query_start_time > DATEADD(day,-7,CURRENT_TIMESTAMP())
  AND ARRAY_TO_STRING(direct_objects_accessed::ARRAY,',') ILIKE '%CUSTOMERS%';
-- Privileges
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.GRANTS_TO_ROLES WHERE GRANTED_ON='TABLE' AND NAME='CUSTOMERS' AND DELETED_ON IS NULL;
-- Users with ACCOUNTADMIN
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.GRANTS_TO_USERS WHERE ROLE='ACCOUNTADMIN' AND DELETED_ON IS NULL;
```
## Post-checks
- Schedule these as weekly reports/alerts. Retention: ACCOUNT_USAGE keeps 365 days.
