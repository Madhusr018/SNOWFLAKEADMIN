# SSO (SAML) & SCIM

## Concept
SAML2 federated login via IdP (Okta, Azure AD). SCIM auto-provisions users/roles from the IdP. OAuth security integrations support external tools.

## Pre-checks
- IdP metadata: issuer, SSO URL, X.509 cert. Decide the default role mapping.
- Keep a local admin login.

## Commands
```sql
USE ROLE ACCOUNTADMIN;
CREATE SECURITY INTEGRATION okta_sso
  TYPE = SAML2 ENABLED = TRUE
  SAML2_ISSUER = 'http://www.okta.com/xxxx'
  SAML2_SSO_URL = 'https://company.okta.com/app/snowflake/xxxx/sso/saml'
  SAML2_PROVIDER = 'OKTA'
  SAML2_X509_CERT = '<CERT_BASE64>'
  SAML2_SP_INITIATED_LOGIN_PAGE_LABEL = 'Company SSO';

-- SCIM
CREATE ROLE okta_provisioner;
GRANT CREATE USER ON ACCOUNT TO ROLE okta_provisioner;
GRANT CREATE ROLE ON ACCOUNT TO ROLE okta_provisioner;
GRANT ROLE okta_provisioner TO ROLE ACCOUNTADMIN;
CREATE SECURITY INTEGRATION okta_scim TYPE = SCIM SCIM_CLIENT = 'OKTA' RUN_AS_ROLE = 'OKTA_PROVISIONER';
SELECT SYSTEM$GENERATE_SCIM_ACCESS_TOKEN('OKTA_SCIM');   -- paste into IdP
```
## Post-checks
```sql
SHOW SECURITY INTEGRATIONS;  DESC SECURITY INTEGRATION okta_sso;
```
- Test SSO login in a private window; check LOGIN_HISTORY (`FIRST_AUTHENTICATION_FACTOR`).

## Gotchas
- SCIM tokens expire (roughly 6 months); rotate them.
- Snowflake login name must match the IdP NameID.
