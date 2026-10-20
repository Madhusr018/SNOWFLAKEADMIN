# Editions

| Edition | Highlights |
|---------|-----------|
| Standard | Core SQL, 1-day Time Travel, Fail-safe 7d, secure data sharing, SSO |
| Enterprise | Multi-cluster warehouses, Time Travel up to 90 d, masking, row access, tagging, search optimization, materialized views, query acceleration |
| Business Critical | Customer-managed keys (Tri-Secret Secure), PHI/HIPAA/PCI support, private connectivity, failover/failback |
| Virtual Private Snowflake (VPS) | Dedicated, isolated environment |

## Pre-checks
```sql
SHOW ORGANIZATION ACCOUNTS;   -- as ORGADMIN; shows edition per account
```
## Post-checks
- Before designing governance or DR, confirm the edition supports the feature (otherwise the DDL errors out).
