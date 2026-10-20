# Custom Roles, Grants, Future Grants

## Concept
Privileges are granted to **roles**, roles to **users** or other roles. Pattern:
- **Access roles** (object privileges, e.g. `SALES_DB_RO`, `SALES_DB_RW`)
- **Functional roles** (job functions, e.g. `ANALYST`, `ETL_ENGINEER`) that receive access roles.

## Pre-checks
```sql
SHOW ROLES LIKE 'SALES%';
SHOW GRANTS ON DATABASE sales_db;
```
## Commands
```sql
USE ROLE USERADMIN;
CREATE ROLE sales_db_ro;  CREATE ROLE sales_db_rw;  CREATE ROLE analyst;

USE ROLE SECURITYADMIN;   -- or object owner
GRANT USAGE ON DATABASE sales_db TO ROLE sales_db_ro;
GRANT USAGE ON ALL SCHEMAS IN DATABASE sales_db TO ROLE sales_db_ro;
GRANT SELECT ON ALL TABLES IN DATABASE sales_db TO ROLE sales_db_ro;
-- Future objects
GRANT USAGE ON FUTURE SCHEMAS IN DATABASE sales_db TO ROLE sales_db_ro;
GRANT SELECT ON FUTURE TABLES IN DATABASE sales_db TO ROLE sales_db_ro;
-- Hierarchy
GRANT ROLE sales_db_ro TO ROLE analyst;
GRANT ROLE analyst TO ROLE SYSADMIN;
GRANT ROLE analyst TO USER john;
-- Warehouse access
GRANT USAGE ON WAREHOUSE bi_wh TO ROLE analyst;
-- Ownership transfer
GRANT OWNERSHIP ON TABLE sales_db.public.t TO ROLE sales_db_rw COPY CURRENT GRANTS;
REVOKE SELECT ON ALL TABLES IN DATABASE sales_db FROM ROLE sales_db_ro;
```
## Post-checks
```sql
SHOW GRANTS TO ROLE analyst;
SHOW GRANTS TO USER john;
SHOW FUTURE GRANTS IN DATABASE sales_db;
-- test as the role
USE ROLE analyst; SELECT * FROM sales_db.public.t LIMIT 1;
```
## Gotchas
- Object privileges need **USAGE on database and schema** too.
- Schema-level future grants override database-level future grants for the same role/type.
- Use *database roles* (`CREATE DATABASE ROLE`) for portable access roles, especially for shares.
- Secondary roles let a session combine privileges: `ALTER USER john SET DEFAULT_SECONDARY_ROLES = ('ALL');`
