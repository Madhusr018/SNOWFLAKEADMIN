# Databases, Schemas, Tables

## Concept
Hierarchy: Account → Database → Schema → Objects (tables, views, stages, streams, tasks, pipes, functions...).
Table types: **Permanent** (Time Travel up to 90 d + 7 d Fail-safe), **Transient** (max 1 d TT, no Fail-safe), **Temporary** (session only), **External** (data in cloud storage), **Dynamic** (declarative, auto-refreshed), **Iceberg** (open table format).
Views: standard, secure, materialized (Enterprise+).

## Pre-checks
```sql
SHOW DATABASES;  SHOW SCHEMAS IN DATABASE sales_db;
SELECT CURRENT_DATABASE(), CURRENT_SCHEMA();
```
## Commands
```sql
USE ROLE SYSADMIN;
CREATE DATABASE sales_db DATA_RETENTION_TIME_IN_DAYS = 7;
CREATE SCHEMA sales_db.raw;
CREATE TRANSIENT SCHEMA sales_db.staging DATA_RETENTION_TIME_IN_DAYS = 1;  -- cheaper for staging
CREATE TABLE sales_db.raw.orders (id NUMBER, region STRING, amount NUMBER(12,2), created_at TIMESTAMP_NTZ);
CREATE TRANSIENT TABLE sales_db.staging.tmp_orders LIKE sales_db.raw.orders;
CREATE SECURE VIEW sales_db.raw.v_orders AS SELECT * FROM sales_db.raw.orders;
ALTER TABLE sales_db.raw.orders SET DATA_RETENTION_TIME_IN_DAYS = 14;
ALTER DATABASE sales_db RENAME TO sales_db_v2;
GRANT ... -- see rbac notes
```
## Post-checks
```sql
SHOW TABLES IN SCHEMA sales_db.raw;   -- retention_time, kind, owner
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS WHERE TABLE_CATALOG='SALES_DB' ORDER BY ACTIVE_BYTES DESC LIMIT 20;
```
## Gotchas
- Use **transient** for ETL staging/reproducible data to avoid Fail-safe cost.
- Snowflake constraints (PK/FK/UNIQUE) are informational only, except NOT NULL, which is enforced.
