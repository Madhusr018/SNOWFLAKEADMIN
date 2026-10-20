# Secure Data Sharing, Reader Accounts, Marketplace

## Concept
Provider shares live, read-only data with consumers: no copy, no ETL. Consumers create a database from the share. Non-Snowflake customers get a **reader account** (provider pays compute). Cross-region/cloud sharing requires replication.

## Pre-checks
- Data to share is in a secure-view or table; confirm sensitive columns excluded.
- Consumer account identifier: `<org>.<account>`.
```sql
SHOW SHARES;
```
## Commands (provider)
```sql
USE ROLE ACCOUNTADMIN;   -- or role with CREATE SHARE
CREATE SHARE sales_share;
GRANT USAGE ON DATABASE sales_db TO SHARE sales_share;
GRANT USAGE ON SCHEMA sales_db.shared TO SHARE sales_share;
GRANT SELECT ON VIEW sales_db.shared.v_orders_secure TO SHARE sales_share;
ALTER SHARE sales_share ADD ACCOUNTS = myorg.partner_acct;
-- Reader account
CREATE MANAGED ACCOUNT partner_reader ADMIN_NAME='admin', ADMIN_PASSWORD='<PWD>', TYPE=READER;
```
## Commands (consumer)
```sql
CREATE DATABASE partner_sales FROM SHARE provider_org.provider_acct.sales_share;
GRANT IMPORTED PRIVILEGES ON DATABASE partner_sales TO ROLE analyst;
```
## Post-checks
```sql
SHOW GRANTS TO SHARE sales_share;
DESC SHARE sales_share;
SELECT * FROM partner_sales.shared.v_orders_secure LIMIT 5;   -- consumer
```
## Gotchas
- Only **secure** views can be shared (not regular views).
- Revoke access: `ALTER SHARE sales_share REMOVE ACCOUNTS = ...;`
