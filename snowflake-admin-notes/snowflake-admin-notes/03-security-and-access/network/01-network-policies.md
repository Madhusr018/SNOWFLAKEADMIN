# Network Policies & Network Rules

## Concept
Allow/block IPs for the account or a user. Modern approach: **network rules** (IP ranges, VPC endpoint IDs) referenced by a policy. A user-level policy overrides the account-level one.

## Pre-checks
- Gather corporate/VPN egress CIDRs and ETL tool IPs. **Include your own current IP** or you can lock yourself out.
```sql
SELECT CURRENT_IP_ADDRESS();
SHOW NETWORK POLICIES;
```
## Commands
```sql
USE ROLE SECURITYADMIN;
CREATE NETWORK RULE sec_db.net.corp_ips TYPE = IPV4 MODE = INGRESS VALUE_LIST = ('203.0.113.0/24','198.51.100.7');
CREATE NETWORK POLICY corp_policy ALLOWED_NETWORK_RULE_LIST = ('SEC_DB.NET.CORP_IPS') COMMENT = 'corp only';
-- classic form:
-- CREATE NETWORK POLICY corp_policy ALLOWED_IP_LIST = ('203.0.113.0/24') BLOCKED_IP_LIST = ();
ALTER ACCOUNT SET NETWORK_POLICY = corp_policy;
ALTER USER svc_etl SET NETWORK_POLICY = etl_policy;   -- user-level
```
## Post-checks
```sql
SHOW PARAMETERS LIKE 'NETWORK_POLICY' IN ACCOUNT;
DESC NETWORK POLICY corp_policy;
```
- Test from an allowed and a disallowed IP; watch LOGIN_HISTORY for errors.

## Rollback
```sql
ALTER ACCOUNT UNSET NETWORK_POLICY;
```
## Gotchas
- Blocked list takes precedence over allowed list.
- For private connectivity use VPCE-based rules.
