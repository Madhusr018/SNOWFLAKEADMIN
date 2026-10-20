# Time Travel, Fail-safe, UNDROP

## Concept
- **Time Travel**: query/clone/restore past data. Standard: 0–1 day; Enterprise+: 0–90 days.
- **Fail-safe**: 7 days after Time Travel for permanent tables, recoverable only by Snowflake Support (not user-accessible).
- Storage cost applies for changed data held in both.

## Pre-checks
```sql
SHOW TABLES LIKE 'ORDERS' IN SCHEMA sales_db.raw;                -- retention_time
SELECT CURRENT_TIMESTAMP();
```
## Commands
```sql
SELECT * FROM orders AT (TIMESTAMP => '2026-10-01 10:00:00'::TIMESTAMP_LTZ);
SELECT * FROM orders AT (OFFSET => -60*30);                       -- 30 min ago
SELECT * FROM orders BEFORE (STATEMENT => '<query_id>');          -- before a bad DML

-- Restore by clone
CREATE TABLE orders_restored CLONE orders AT (OFFSET => -3600);
-- Dropped objects
DROP TABLE orders;
UNDROP TABLE orders;   UNDROP SCHEMA raw;   UNDROP DATABASE sales_db;
SHOW TABLES HISTORY IN SCHEMA sales_db.raw;
-- Replace pattern: clone, then swap
ALTER TABLE orders SWAP WITH orders_restored;
```
## Post-checks
```sql
SELECT COUNT(*) FROM orders;  -- compare with expected
SHOW TABLES LIKE 'ORDERS%';
```
## Gotchas
- Name collision blocks UNDROP; rename the new object first.
- Setting retention to 0 disables Time Travel (and future recovery).
- A table loses history once beyond its retention period, so act quickly.
