# Infrastructure as Code & CI/CD

## Concept
- **Terraform** (Snowflake provider): manage warehouses, roles, grants, users, policies declaratively.
- **schemachange / Flyway / dbt**: versioned DB object and data transformations deployed through CI.
- Use service users with key-pair auth in pipelines; separate dev/test/prod accounts or databases (use clone for test).

## Suggested repo layout
```
infra/        # terraform: roles, warehouses, grants, network policies
migrations/   # V1.0.0__create_tables.sql, R__views.sql (schemachange)
.github/workflows/deploy.yml
```
## Commands
```bash
terraform init && terraform plan && terraform apply
schemachange deploy -f migrations -a myorg-acct -u svc_deploy -r deploy_role -w deploy_wh -c db.schema.CHANGE_HISTORY --create-change-history-table
```
## Post-checks
- `terraform plan` shows no drift; CHANGE_HISTORY table lists the latest version.
- Never store credentials/keys in git (use CI secrets).
