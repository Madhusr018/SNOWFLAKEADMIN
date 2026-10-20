# Session Policies & Private Connectivity

## Session policy
Controls idle timeout for sessions.
```sql
CREATE SESSION POLICY sec_db.policies.sess_pol SESSION_IDLE_TIMEOUT_MINS = 30 SESSION_UI_IDLE_TIMEOUT_MINS = 15;
ALTER ACCOUNT SET SESSION POLICY sec_db.policies.sess_pol;
```
## Private connectivity
AWS PrivateLink / Azure Private Link / GCP Private Service Connect (Business Critical+).
```sql
SELECT SYSTEM$GET_PRIVATELINK_CONFIG();
SELECT SYSTEM$AUTHORIZE_PRIVATELINK('<aws_account_id>', '<federated_token>');
```
## Post-checks
- Verify DNS resolves the privatelink URL to private IPs from inside your VPC.
