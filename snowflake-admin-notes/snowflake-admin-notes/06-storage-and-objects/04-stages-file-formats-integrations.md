# Stages, File Formats, Storage Integrations

## Concept
- **Internal stages**: user (`@~`), table (`@%t`), named (`@my_stage`).
- **External stages**: S3/Azure/GCS, accessed through a **storage integration** (no embedded credentials; preferred).
- **File format** objects define CSV/JSON/Parquet parsing.

## Pre-checks
- Cloud-side: IAM role/trust policy (AWS), service principal (Azure) or service account (GCP) ready.
```sql
SHOW INTEGRATIONS;  SHOW STAGES;  SHOW FILE FORMATS;
```
## Commands (AWS example)
```sql
USE ROLE ACCOUNTADMIN;
CREATE STORAGE INTEGRATION s3_int TYPE = EXTERNAL_STAGE STORAGE_PROVIDER = 'S3' ENABLED = TRUE
  STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::123456789012:role/snowflake_role'
  STORAGE_ALLOWED_LOCATIONS = ('s3://my-bucket/landing/');
DESC INTEGRATION s3_int;     -- copy STORAGE_AWS_IAM_USER_ARN and STORAGE_AWS_EXTERNAL_ID into the AWS trust policy
GRANT USAGE ON INTEGRATION s3_int TO ROLE sysadmin;

USE ROLE SYSADMIN;
CREATE FILE FORMAT sales_db.raw.csv_ff TYPE = CSV FIELD_DELIMITER = ',' SKIP_HEADER = 1
  FIELD_OPTIONALLY_ENCLOSED_BY = '"' NULL_IF = ('', 'NULL');
CREATE STAGE sales_db.raw.landing URL = 's3://my-bucket/landing/' STORAGE_INTEGRATION = s3_int FILE_FORMAT = sales_db.raw.csv_ff;
CREATE STAGE sales_db.raw.int_stage ENCRYPTION = (TYPE = 'SNOWFLAKE_SSE');
```
## Post-checks
```sql
LIST @sales_db.raw.landing;                     -- proves access works
SELECT $1,$2 FROM @sales_db.raw.landing (FILE_FORMAT => 'sales_db.raw.csv_ff') LIMIT 5;
```
## Gotchas
- A trust-policy typo is the #1 cause of "Access Denied". Re-check after any `CREATE OR REPLACE INTEGRATION` (the external ID changes).
- Use `ALTER ACCOUNT SET REQUIRE_STORAGE_INTEGRATION_FOR_STAGE_CREATION = TRUE` to enforce.
