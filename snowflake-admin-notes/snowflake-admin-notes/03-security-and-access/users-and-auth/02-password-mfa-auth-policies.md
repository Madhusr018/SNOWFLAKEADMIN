# Password Policy, MFA, Authentication Policy

## Concept
- **Password policy**: complexity, age, lockout.
- **MFA**: for PERSON users using passwords. Snowflake is enforcing MFA for password sign-ins, so plan for it.
- **Authentication policy**: controls allowed auth methods, client types and MFA requirements per account or user.

## Pre-checks
```sql
SHOW PASSWORD POLICIES;  SHOW AUTHENTICATION POLICIES;
SELECT NAME, HAS_MFA FROM SNOWFLAKE.ACCOUNT_USAGE.USERS WHERE HAS_MFA = FALSE AND DELETED_ON IS NULL;
```
## Commands
```sql
CREATE PASSWORD POLICY sec_db.policies.pwd_policy
  PASSWORD_MIN_LENGTH = 14 PASSWORD_MIN_UPPER_CASE_CHARS = 1 PASSWORD_MIN_LOWER_CASE_CHARS = 1
  PASSWORD_MIN_NUMERIC_CHARS = 1 PASSWORD_MIN_SPECIAL_CHARS = 1
  PASSWORD_MAX_AGE_DAYS = 90 PASSWORD_MAX_RETRIES = 5 PASSWORD_LOCKOUT_TIME_MINS = 30 PASSWORD_HISTORY = 5;
ALTER ACCOUNT SET PASSWORD POLICY sec_db.policies.pwd_policy;

CREATE AUTHENTICATION POLICY sec_db.policies.auth_policy
  AUTHENTICATION_METHODS = ('SAML','PASSWORD','KEYPAIR')
  MFA_ENROLLMENT = REQUIRED;
ALTER ACCOUNT SET AUTHENTICATION POLICY sec_db.policies.auth_policy;
-- user-level override (e.g. service user key-pair only)
ALTER USER svc_etl SET AUTHENTICATION POLICY sec_db.policies.svc_auth;
```
## Post-checks
```sql
SELECT * FROM TABLE(SEC_DB.INFORMATION_SCHEMA.POLICY_REFERENCES(POLICY_NAME=>'SEC_DB.POLICIES.PWD_POLICY'));
```
## Gotchas
- Keep a break-glass ACCOUNTADMIN that can still log in if SSO fails; test before enforcing.
- Authentication policy options are evolving; confirm exact syntax in the current docs.
