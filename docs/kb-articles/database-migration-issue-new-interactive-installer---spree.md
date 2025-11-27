# Database Migration Issue: New Interactive Installer - spree

---

# Database Migration Issue: New Interactive Installer

> **Problem Statement:** Users may experience migration failures or data integrity issues when upgrading without adjusting their database schemas for changes introduced by the new interactive installer.

---

## Applies To

- **Product/Repository:** spree
- **Language/Framework:** None
- **Versions:** All versions
- **Environment:** Both Development and Production

---

## Symptoms

Users experiencing this issue may observe:

- **Error Messages:**
  ```
  ActiveRecord::MigrationError: Column 'new_column' cannot be null
  ```
  ```
  ActiveRecord::StatementInvalid: PG::UndefinedTable: ERROR:  relation "new_table" does not exist
  ```

- **Behavior:**
  - Database migrations fail during the upgrade process, preventing access to essential features.
  - Data integrity issues arise, such as missing or corrupted data after the installation process.
  - Users may find that new features related to the interactive installer, such as admin authentication, do not function as expected.

- **Impact:**
  - Users may be unable to proceed with the upgrade, leading to service disruptions.
  - Continued reliance on outdated features may lead to security vulnerabilities and performance degradation.

---

## Root Cause

### Technical Explanation

Migration failures and data integrity issues arise due to schema changes implemented in the new interactive installer. Users who attempt to upgrade without executing the necessary database migration scripts may encounter problems, including newly introduced columns that are either missing or incorrectly configured.

**Key factors:**
- New database columns and tables added by the interactive installer are not aligned with existing schemas.
- Removal of deprecated columns or tables that existing queries reference can lead to runtime errors.
- Changes in database indexing and relationships that do not match the previous database design can cause integrity issues and SQL errors.

---

## Resolution

### Migration Guide (For Code-Level Changes)

**IMPORTANT:** If this issue is due to code changes, please follow this migration guide:

#### Before (Old Code/Config):
```ruby
# Example: Missing the new column required for user authentication
def change
  create_table :users do |t|
    t.string :username
    t.string :password_digest
    # t.datetime :last_logged_in  # Removed in new installer
  end
end
```

#### After (New Code/Config):
```ruby
# Updated migration including new required fields
def change
  create_table :users do |t|
    t.string :username
    t.string :password_digest
    t.datetime :last_logged_in  # Added in new installer
    t.string :email, null: false  # New required field
  end

  add_index :users, :email, unique: true  # New index for email
end
```

#### What Changed:
- The `last_logged_in` column was added to track user activity.
- An `email` column is now mandatory for user authentication.
- An index on the `email` column has been added to enforce uniqueness and improve lookup performance.

---

### Step-by-Step Fix

**Method 1: Database Migration**

Follow these steps to resolve the issue:

1. **Run the Migration Files**
   ```bash
   rails db:migrate
   ```

2. **Check for Migration Errors**
   - Look for any migration errors in the console output.
   - Correct the migration files as necessary based on the error messages.

3. **Update Existing Database Records**
   ```ruby
   User.update_all(last_logged_in: Time.current)  # Update new column with default values
   ```

**Expected Result:**
After running the migrations successfully, the database should reflect the new schema, allowing users to authenticate properly with the latest features.

**Verification:**
```bash
rails db:rollback  # Verify that rollbacks can be performed without errors
Rails console -e production  # Use console to check if new columns/data are present
```

---

**Method 2: Manual Schema Adjustment** *(If primary method not feasible)*

1. **Backup Your Database**
   ```bash
   pg_dump your_database > your_database_backup.sql  # Or appropriate command for your DB
   ```

2. **Manually Alter Tables**
   ```sql
   ALTER TABLE users ADD COLUMN email VARCHAR(255) NOT NULL;
   CREATE UNIQUE INDEX index_users_on_email ON users (email);
   ```

3. **Verify Changes in Database**
   ```sql
   SELECT * FROM users LIMIT 10;  # Check if new columns are correctly created
   ```

---

## Workarounds

If the above fix cannot be applied immediately:

**Temporary Solution:**
- Revert to the last stable version of the application that does not require the new schema.
- Disable the upgrade process until the database scheme can be adjusted.

**Note:** This is a temporary measure. Apply the full resolution when possible.

---

## Prevention & Best Practices

To avoid this issue in the future:

1. **Regular Database Backups**
   - Ensure to take regular backups before running migrations to prevent data loss.

2. **Thoroughly Review Migration Files**
   - Before applying any migrations, review the changes in migration files to ensure compatibility with existing schemas.

3. **Test Migrations in Staging**
   - Always test migration scripts in a staging environment similar to production to catch issues before they affect live users.

---

## References

- **Related GitHub Issues:** [#13261](https://github.com/spree/spree/issues/13261)
- **Pull Requests:** [#13266](https://github.com/spree/spree/pull/13266)
- **Documentation:** [Spree Migration Documentation](https://guides.spreecommerce.org/)
- **Related Articles:** [Troubleshooting Database Migrations](https://support.yourcompany.com/troubleshooting-database-migrations)

---

## Related Articles

- [Best Practices for Database Migrations in Spree]
- [Configuration Guide for Interactive Installer]
- [Common Issues with Upgrading Spree]

---

## Support Escalation

If this article does not resolve your issue:

1. **Gather the following information:**
   - Full error message and stack trace
   - Environment details (OS, versions, configurations)
   - Steps to reproduce the issue
   - Logs from: `log/production.log`, `log/development.log`

2. **Contact Support:**
   - Submit via: [Contact Us Page]
   - Include: All information from step 1
   - Reference: This KB article

---

*Last Updated: October 2023*  
*Article ID: 12345*

---