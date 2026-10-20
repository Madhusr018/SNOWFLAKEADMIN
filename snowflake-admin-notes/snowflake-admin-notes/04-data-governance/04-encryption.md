# Encryption

## Concept
All data encrypted at rest (AES-256) and in transit (TLS 1.2+). Hierarchical key model with automatic key rotation (every 30 days) and re-keying (annual, Enterprise+). **Tri-Secret Secure** (Business Critical+) combines a customer-managed key with a Snowflake key.

## Commands
```sql
ALTER ACCOUNT SET PERIODIC_DATA_REKEYING = TRUE;     -- Enterprise+
SHOW PARAMETERS LIKE 'PERIODIC_DATA_REKEYING' IN ACCOUNT;
```
## Gotchas
- With Tri-Secret Secure, revoking your key makes data inaccessible; protect the CMK and keep backups of access policy.
