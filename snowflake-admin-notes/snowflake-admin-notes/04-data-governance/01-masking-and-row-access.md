# Dynamic Data Masking & Row Access Policies (Enterprise+)

## Concept
- **Masking policy**: column-level, evaluated at query time based on role/context.
- **Row access policy**: row-level filter, usually via a mapping table.
Policies are schema-level objects; create them in a central governance schema.

## Pre-checks
```sql
SHOW MASKING POLICIES IN ACCOUNT;  SHOW ROW ACCESS POLICIES IN ACCOUNT;
```
- Dedicated role (e.g. `GOVERNANCE_ADMIN`) with `CREATE MASKING POLICY`, `APPLY MASKING POLICY` on account.

## Commands
```sql
CREATE MASKING POLICY gov_db.policies.mask_email AS (val STRING) RETURNS STRING ->
  CASE WHEN IS_ROLE_IN_SESSION('PII_READER') THEN val
       ELSE REGEXP_REPLACE(val, '.+@', '*****@') END;
ALTER TABLE sales_db.public.customers MODIFY COLUMN email SET MASKING POLICY gov_db.policies.mask_email;
ALTER TABLE sales_db.public.customers MODIFY COLUMN email UNSET MASKING POLICY;

CREATE ROW ACCESS POLICY gov_db.policies.region_rap AS (region STRING) RETURNS BOOLEAN ->
  EXISTS (SELECT 1 FROM gov_db.policies.role_region_map m
          WHERE m.role_name = CURRENT_ROLE() AND m.region = region)
  OR IS_ROLE_IN_SESSION('SYSADMIN');
ALTER TABLE sales_db.public.orders ADD ROW ACCESS POLICY gov_db.policies.region_rap ON (region);
ALTER TABLE sales_db.public.orders DROP ROW ACCESS POLICY gov_db.policies.region_rap;
```
## Post-checks
```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.POLICY_REFERENCES(POLICY_NAME => 'GOV_DB.POLICIES.MASK_EMAIL'));
USE ROLE analyst;  SELECT email FROM sales_db.public.customers LIMIT 5;     -- masked
USE ROLE pii_reader; SELECT email FROM sales_db.public.customers LIMIT 5;   -- clear
```
## Gotchas
- Prefer `IS_ROLE_IN_SESSION()` over `CURRENT_ROLE()` to honor role hierarchy.
- A column can have one masking policy; a table one row access policy at a time.
- Row access policies can add query overhead; keep the logic simple.
