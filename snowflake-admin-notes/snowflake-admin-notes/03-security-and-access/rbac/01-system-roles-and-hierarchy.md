# System Roles & Hierarchy

## Concept
| Role | Purpose |
|------|---------|
| ORGADMIN | Org-level: accounts, org usage |
| ACCOUNTADMIN | Top account role (SYSADMIN + SECURITYADMIN). Billing, parameters, resource monitors. Use rarely. |
| SECURITYADMIN | Manage grants globally (MANAGE GRANTS); inherits USERADMIN |
| USERADMIN | Create/manage users and roles |
| SYSADMIN | Create warehouses, databases and objects |
| PUBLIC | Granted automatically to every user/role |

Best practice: all custom roles roll up to SYSADMIN so admins can see all objects.

```
ACCOUNTADMIN
 ├─ SECURITYADMIN ── USERADMIN
 └─ SYSADMIN ── custom functional roles ── access roles
```
## Pre-checks
```sql
SHOW GRANTS TO ROLE ACCOUNTADMIN;
SHOW GRANTS OF ROLE ACCOUNTADMIN;     -- who has it
```
## Post-checks
- Only 2–3 users hold ACCOUNTADMIN; no service accounts have it.
