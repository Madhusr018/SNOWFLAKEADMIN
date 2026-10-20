# Day-One Checklist (in order)

1. [ ] Secure ACCOUNTADMIN: MFA on, 2+ admins, no daily use, real email set.
2. [ ] Set account parameters (timezone, timeouts, retention) → `01-account-parameters.md`
3. [ ] Design role hierarchy (functional + access roles) → `03-security-and-access/rbac`
4. [ ] SSO/SCIM, password & authentication policies → `users-and-auth`
5. [ ] Network policy (account level) → `network`
6. [ ] Storage integrations (no keys in stages) → `07-data-loading`
7. [ ] Warehouses per workload + resource monitors → `05-compute`
8. [ ] Governance: tags, masking, row access → `04-data-governance`
9. [ ] Monitoring views, alerts, budgets → `09-monitoring-and-cost`
10. [ ] DR: replication/failover plan → `11-business-continuity`

## Quick verification
```sql
SELECT CURRENT_ROLE(), CURRENT_USER();
SHOW USERS;  SHOW ROLES;  SHOW WAREHOUSES;  SHOW INTEGRATIONS;  SHOW NETWORK POLICIES;  SHOW RESOURCE MONITORS;
```
