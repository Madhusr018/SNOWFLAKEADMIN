# Troubleshooting Cheat Sheet

| Error / Symptom | Likely cause | Check / Fix |
|-----------------|--------------|-------------|
| `Insufficient privileges to operate on ...` | Missing grant / wrong role | `SHOW GRANTS TO ROLE x`; `SELECT CURRENT_ROLE()`; grant USAGE on db+schema |
| `No active warehouse selected` | No default warehouse | `USE WAREHOUSE x` / set DEFAULT_WAREHOUSE |
| `IP x is not allowed to access Snowflake` | Network policy | `SHOW NETWORK POLICIES`; add IP; LOGIN_HISTORY |
| Account locked / too many failed logins | Lockout | `ALTER USER x SET MINS_TO_UNLOCK=0` |
| Query stuck/queued | Warehouse overloaded | `SHOW WAREHOUSES`; multi-cluster/resize; cancel with `SYSTEM$CANCEL_QUERY` |
| Warehouse not resuming | Suspended by resource monitor | `SHOW RESOURCE MONITORS`; raise quota |
| `Access Denied` on external stage | IAM trust policy mismatch | `DESC INTEGRATION`; update external ID/ARN |
| COPY loads 0 files | Already loaded (64-day metadata) / wrong path | `COPY_HISTORY`, `LIST @stage`, `FORCE=TRUE` if intended |
| Snowpipe not loading | Event notification missing | `SYSTEM$PIPE_STATUS`; verify SQS config |
| Task not running | Suspended / no privilege | `SHOW TASKS` state; `ALTER TASK RESUME`; EXECUTE TASK grant |
| Stream stale | Not consumed within retention | Recreate stream; extend `MAX_DATA_EXTENSION_TIME_IN_DAYS` |
| `Object does not exist or not authorized` | Wrong db/schema context or no USAGE | `SELECT CURRENT_DATABASE(), CURRENT_SCHEMA()` |
| Bill spike | Large warehouse/long-running/serverless | `METERING_DAILY_HISTORY`, `QUERY_HISTORY` |

## Handy diagnostics
```sql
SELECT CURRENT_ROLE(), CURRENT_WAREHOUSE(), CURRENT_DATABASE(), CURRENT_SCHEMA(), CURRENT_USER();
SELECT SYSTEM$ALLOWLIST();            -- hosts/ports Snowflake clients need
SELECT SYSTEM$WHITELIST_PRIVATELINK();
SHOW LOCKS;  SHOW TRANSACTIONS;
```
