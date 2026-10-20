# Managed Access Schemas

## Concept
In a managed access schema only the schema owner (or a role with MANAGE GRANTS) can grant privileges on objects, not the object owners. Good for central governance.

## Commands
```sql
CREATE SCHEMA sales_db.curated WITH MANAGED ACCESS;
ALTER SCHEMA sales_db.raw ENABLE MANAGED ACCESS;
```
## Post-checks
```sql
SHOW SCHEMAS IN DATABASE sales_db;   -- options column shows MANAGED ACCESS
```
