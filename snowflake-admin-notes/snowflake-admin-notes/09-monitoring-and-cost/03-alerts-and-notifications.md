# Alerts & Notifications

## Concept
`CREATE ALERT` runs a condition on a schedule and executes an action (e.g. send email). Needs a notification integration.

## Commands
```sql
CREATE NOTIFICATION INTEGRATION email_int TYPE=EMAIL ENABLED=TRUE ALLOWED_RECIPIENTS=('dba@example.com');
CREATE ALERT credit_spike WAREHOUSE = admin_wh SCHEDULE = '60 MINUTE'
IF (EXISTS (SELECT 1 FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
            WHERE start_time > DATEADD(hour,-1,CURRENT_TIMESTAMP()) GROUP BY warehouse_name HAVING SUM(credits_used) > 20))
THEN CALL SYSTEM$SEND_EMAIL('email_int','dba@example.com','Credit spike','Check warehouses');
ALTER ALERT credit_spike RESUME;      -- alerts are created suspended
```
## Post-checks
```sql
SHOW ALERTS;
SELECT * FROM TABLE(INFORMATION_SCHEMA.ALERT_HISTORY()) ORDER BY SCHEDULED_TIME DESC LIMIT 10;
```
