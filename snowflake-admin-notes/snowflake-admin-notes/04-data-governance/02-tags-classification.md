# Object Tagging & Data Classification

## Concept
Tags are key/value metadata attached to objects/columns (e.g. `PII='email'`). Tag-based masking applies a policy automatically to every column carrying a tag. Classification auto-detects semantic/privacy categories (Enterprise+).

## Commands
```sql
CREATE TAG gov_db.tags.pii ALLOWED_VALUES 'email','phone','ssn';
ALTER TABLE sales_db.public.customers MODIFY COLUMN email SET TAG gov_db.tags.pii = 'email';
ALTER TAG gov_db.tags.pii SET MASKING POLICY gov_db.policies.mask_email;   -- tag-based masking (policy matches data type)

-- Classification
SELECT SYSTEM$CLASSIFY('sales_db.public.customers', {'auto_tag': true});
```
## Post-checks
```sql
SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.TAG_REFERENCES WHERE TAG_NAME='PII';
SELECT SYSTEM$GET_TAG('gov_db.tags.pii','sales_db.public.customers.email','COLUMN');
```
## Gotchas
- ACCOUNT_USAGE views lag (up to ~2 h); use `INFORMATION_SCHEMA.TAG_REFERENCES()` for real-time.
