# Snowflake Data Connection Documentation

This file provides instructions on how to create a read-only user in Snowflake and what information to send to your Anova rep so we can create your data connection.

# Create Read-Only User With Access to Schema

For security, we recommend you create a separate user for Anova that has read-only access to the relevant database, as Anova only needs to run SELECT queries. This user **must** be able to query for the database schema so we can generate the semantic layer. You can refer to this link for specific instructions: https://docs.snowflake.com/en/user-guide/security-access-control-configure#creating-custom-read-only-roles.

## SQLs to Run

```sql
-- 1. Create role
CREATE ROLE IF NOT EXISTS anova_role;

-- 2. Grant USAGE
GRANT USAGE ON WAREHOUSE <w>     TO ROLE anova_role;
GRANT USAGE ON DATABASE  <d>     TO ROLE anova_role;
GRANT USAGE ON SCHEMA    <d>.<s> TO ROLE anova_role;

-- 3. Grant SELECT on TABLES, VIEWS, and MATERIALIZED VIEWS (whatever is needed)
GRANT SELECT ON ALL TABLES             IN SCHEMA <d>.<s> TO ROLE anova_role;
GRANT SELECT ON ALL VIEWS              IN SCHEMA <d>.<s> TO ROLE anova_role;
GRANT SELECT ON ALL MATERIALIZED VIEWS IN SCHEMA <d>.<s> TO ROLE anova_role;

-- 4. Grant SELECT on FUTURE TABLES, VIEWS, and MATERIALIZED VIEWS (optional, but recommended)
GRANT SELECT ON FUTURE TABLES             IN SCHEMA <d>.<s> TO ROLE anova_role;
GRANT SELECT ON FUTURE VIEWS              IN SCHEMA <d>.<s> TO ROLE anova_role;
GRANT SELECT ON FUTURE MATERIALIZED VIEWS IN SCHEMA <d>.<s> TO ROLE anova_role;

-- 5. Create user
CREATE USER IF NOT EXISTS anova_user
  PASSWORD = '<strong_password>'
  DEFAULT_ROLE = anova_role
  DEFAULT_WAREHOUSE = <w>
  MUST_CHANGE_PASSWORD = FALSE;

-- 6. Grant role to user
GRANT ROLE anova_role TO USER anova_user;
```

# Send Details to Anova

Once you've completed the above, reach out to your Anova rep and they will outline what is needed and how to securely send the information so that we can create the connection, generate an initial semantic layer, and start the training process.

# Support

If you have any questions or need any help as you do the above, feel free to reach out to your Anova rep or email <support@anova.ai>.