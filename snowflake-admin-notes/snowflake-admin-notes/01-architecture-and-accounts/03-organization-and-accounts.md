# Organization & Accounts

## Concept
An **organization** groups accounts. **ORGADMIN** role manages accounts, org-level usage and replication enablement. Account identifier format: `<orgname>-<account_name>`.

## Pre-checks
- Who holds ORGADMIN? Grant it sparingly (`GRANT ROLE ORGADMIN TO USER x` as ACCOUNTADMIN).

## Commands
```sql
USE ROLE ORGADMIN;
SHOW ORGANIZATION ACCOUNTS;
CREATE ACCOUNT dev_acct
  ADMIN_NAME = admin1
  ADMIN_PASSWORD = '<STRONG_PASSWORD>'
  EMAIL = 'admin@example.com'
  EDITION = ENTERPRISE
  REGION = aws_us_east_1
  MUST_CHANGE_PASSWORD = TRUE;
-- Org-wide usage
SELECT * FROM SNOWFLAKE.ORGANIZATION_USAGE.USAGE_IN_CURRENCY_DAILY ORDER BY USAGE_DATE DESC LIMIT 20;
```
## Post-checks
- `SHOW ORGANIZATION ACCOUNTS;` lists the new account; log in and force admin password change.
- Enable MFA for the new admin immediately.

## Gotchas
- Account names are unique within the org; accounts are not easily renamed (use `ALTER ACCOUNT ... RENAME TO` as ORGADMIN).
