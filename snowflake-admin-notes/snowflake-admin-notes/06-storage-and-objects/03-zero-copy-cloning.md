# Zero-Copy Cloning

## Concept
Metadata-only copy of tables, schemas, databases (and streams, tasks, etc.). Shares micro-partitions until changed, so there is no extra storage up front. Great for dev/test and backups.

## Pre-checks
- Confirm privileges (SELECT on the source tables, CREATE on the target). Confirm retention if cloning `AT` a past time.

## Commands
```sql
CREATE DATABASE dev_db CLONE prod_db;
CREATE SCHEMA dev_db.raw CLONE prod_db.raw;
CREATE TABLE t_backup CLONE t AT (TIMESTAMP => DATEADD(hour,-2,CURRENT_TIMESTAMP()));
CREATE DATABASE dev_db CLONE prod_db COPY GRANTS;   -- privilege handling varies; verify grants after
```
## Post-checks
```sql
SELECT COUNT(*) FROM dev_db.raw.orders;
SHOW GRANTS ON DATABASE dev_db;
```
## Gotchas
- Cloned pipes (with auto-ingest) may be paused/need review; tasks are cloned suspended.
- Clones diverge and then incur their own storage as data changes.
