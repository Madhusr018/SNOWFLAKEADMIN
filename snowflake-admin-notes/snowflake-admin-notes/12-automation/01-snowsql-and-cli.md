# SnowSQL & Snowflake CLI

## Concept
- **SnowSQL**: classic CLI client. **Snowflake CLI (`snow`)**: newer tool for apps, Streamlit, Snowpark, SQL.
- Connection settings in config files; never put secrets in git.

## Commands
```bash
# SnowSQL
snowsql -a myorg-acct -u john -r analyst -w bi_wh -d sales_db -s public
snowsql -c myconn -f script.sql -o output_format=csv -o friendly=false
# Put/Get files
PUT file:///data/orders.csv @sales_db.raw.int_stage AUTO_COMPRESS=TRUE;
GET @sales_db.raw.int_stage/orders.csv.gz file:///tmp/;

# Snowflake CLI
snow connection add
snow connection test
snow sql -q "SELECT CURRENT_VERSION()"
snow sql -f deploy.sql
```
## Post-checks
- `snow connection test` succeeds; query returns expected role/warehouse.
