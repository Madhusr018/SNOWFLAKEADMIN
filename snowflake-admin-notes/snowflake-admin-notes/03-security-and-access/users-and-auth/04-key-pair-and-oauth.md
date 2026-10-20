# Key-Pair Authentication (service accounts)

## Concept
Use an RSA key pair instead of passwords for programmatic access. The private key stays with the client; the public key is stored on the user. Two keys are supported to allow rotation.

## Commands
```bash
openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out rsa_key.p8 -nocrypt
openssl rsa -in rsa_key.p8 -pubout -out rsa_key.pub
```
```sql
ALTER USER svc_etl SET RSA_PUBLIC_KEY = 'MIIBIjANBgkq...';       -- no BEGIN/END lines
-- Rotation
ALTER USER svc_etl SET RSA_PUBLIC_KEY_2 = '<NEW_KEY>';
ALTER USER svc_etl UNSET RSA_PUBLIC_KEY;
DESC USER svc_etl;     -- check RSA_PUBLIC_KEY_FP
```
## Post-checks
- Connect: `snowsql -a <acct> -u svc_etl --private-key-path rsa_key.p8`
- Store the private key in a secrets manager, never in git. Add `*.p8` to `.gitignore`.
