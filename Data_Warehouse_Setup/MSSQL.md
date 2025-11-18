# Microsoft SQL Server Data Connection Documentation

This file provides instructions on how to create a read-only user in Microsoft SQL Server and what information to send to your Anova rep so we can create your data connection.

# Create Read-Only User With Access to Schema

For security, we recommend you create a separate user for Anova that has read-only access to the relevant database, as Anova only needs to run SELECT queries. This user must be able to query for the database schema so we can generate the semantic layer.

## SQLs to Run

```sql
-- 1. Create login at server-level
USE master;
GO

CREATE LOGIN anova_user WITH PASSWORD = '<strong_password>';
GO

-- 2. Set database-level
USE [<database_name>];
GO

-- 3. Create role
CREATE ROLE anova_role;
GO

-- 4. Grant SELECT and VIEW DEFINITION on SCHEMA
GRANT SELECT ON SCHEMA::[<schema_name>] TO anova_role;
GRANT VIEW DEFINITION ON SCHEMA::[<schema_name>] TO anova_role;
GO

-- 5. Create user
CREATE USER anova_user FOR LOGIN anova_user;
GO

-- 6. Add user to role
ALTER ROLE anova_role ADD MEMBER anova_user;
GO
```

# Send Details to Anova

Once you've completed the above, reach out to your Anova rep and they will outline what is needed and how to securely send the information so that we can create the connection, generate an initial semantic layer, and start the training process.

# Support

If you have any questions or need any help as you do the above, feel free to reach out to your Anova rep or email <support@anova.ai>.