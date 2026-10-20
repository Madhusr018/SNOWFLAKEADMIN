# Snowflake Admin Notes

Order-wise admin notes. Follow folders 01 → 13 as you would when setting up and running an account.
Each note has: **Concept → Pre-checks → Commands → Post-checks → Gotchas**.

| # | Folder | What it covers |
|---|--------|----------------|
| 01 | architecture-and-accounts | Architecture, editions, org/accounts |
| 02 | account-setup | Parameters, first-day checklist |
| 03 | security-and-access | RBAC, users, MFA/SSO, key-pair, network policies |
| 04 | data-governance | Masking, row access, tags, access history |
| 05 | compute | Warehouses, multi-cluster, resource monitors |
| 06 | storage-and-objects | Databases, tables, Time Travel, cloning, stages |
| 07 | data-loading | COPY, Snowpipe, streams & tasks |
| 08 | sharing-and-replication | Shares, reader accounts, replication |
| 09 | monitoring-and-cost | ACCOUNT_USAGE, credits, alerts, budgets |
| 10 | performance | Query profile, clustering, caching, SOS |
| 11 | business-continuity | Failover/failback, recovery runbook |
| 12 | automation | SnowSQL, Snowflake CLI, Terraform, schemachange |
| 13 | troubleshooting | Common issues and fixes |
| 14 | cheatsheets | Quick command reference |

> Edition note: features marked **(Enterprise+)** or **(Business Critical+)** need that edition. Always verify against the current Snowflake docs, as features change.
> Convention: replace `<PLACEHOLDERS>`. Test in a non-prod account first.
