# Recovery Runbook (quick reference)

| Situation | Action |
|-----------|--------|
| Dropped table/schema/db | `UNDROP ...` (within retention) |
| Bad UPDATE/DELETE | `CREATE TABLE t_fix CLONE t BEFORE (STATEMENT=>'<qid>')`, validate, then `SWAP WITH` |
| Corrupt/old data beyond TT | Contact Snowflake Support (Fail-safe, permanent tables only, 7 d) |
| Region/cloud outage | Failover group promotion on DR account |
| Compromised user | `ALTER USER x SET DISABLED=TRUE; ALTER USER x ABORT ALL QUERIES;` rotate keys/passwords, review LOGIN_HISTORY |
| Runaway cost | Suspend warehouse, `SYSTEM$CANCEL_QUERY`, apply resource monitor |

## Pre-incident prep
- Retention ≥ 7 days on critical tables; DR tested; contacts documented.
## Post-incident
- Root cause, update runbook, verify grants/objects after restore.
