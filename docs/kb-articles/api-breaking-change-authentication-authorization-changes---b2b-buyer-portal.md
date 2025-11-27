# API Breaking Change: Authentication/Authorization Changes - b2b-buyer-portal

# API Breaking Change: Authentication/Authorization Changes

> **Problem Statement:** Users may experience login failures, permission errors, or token issues due to changes in authentication or authorization logic.

---

## Applies To

- **Product/Repository:** b2b-buyer-portal
- **Language/Framework:** None
- **Versions:** All versions
- **Environment:** Production

---

## Symptoms

Users experiencing this issue may observe:

- **Error Messages:**
  ```
  401 Unauthorized
  403 Forbidden
  Token has expired or is invalid
  ```

- **Behavior:**
  - Users are unable to log in, receiving error messages during authentication attempts.
  - Token refresh operations are failing, leading to session management issues.
  - Users may have access denied errors for resources they previously accessed.

- **Impact:**
  - Users may be locked out of their accounts, reducing their ability to perform necessary transactions through the portal.
  - Significant disruption in workflow for users reliant on the portal for their operations.

---

## Root Cause

### Technical Explanation

The recent changes in authentication and authorization logic have modified how tokens are issued, validated, and refreshed. These adjustments were introduced as part of the enhancements to improve security and maintainability of the codebase.

**Key factors:**
- The introduction of new token validation rules may inadvertently fail older tokens issued prior to the changes.
- Dependencies related to authentication libraries were updated, leading to modifications in how sessions are handled.
- Changes to user roles and permissions logic that affect access to specific resources have not been communicated effectively.

---

## Resolution

### Migration Guide (For Code-Level Changes)

**IMPORTANT:** If this issue is due to code changes (API updates, config changes, breaking changes), provide a migration guide:

#### Before (Old Code/Config):
```javascript
// Old token retrieval process
const token = await authenticateUser(username, password);
// Token validation
if (!isTokenValid(token)) {
  throw new Error("Authentication failed");
}
```

#### After (New Code/Config):
```javascript
// New token retrieval process
const { accessToken } = await requestNewToken(username, password);
// Token validation
if (!isAccessTokenValid(accessToken)) {
  throw new Error("Token has expired or is invalid");
}
```

#### What Changed:
- The function `authenticateUser` has been replaced with `requestNewToken`, reflecting updated logic for issuing tokens.
- Token validation now checks for a broader set of conditions, ensuring expired tokens are not accepted.
- Additional user role checks are now part of the authentication process, possibly resulting in ‘Unauthorized’ errors if roles are not configured correctly.

---

### Step-by-Step Fix

**Method 1: Update Authentication Logic**

Follow these steps to resolve the issue:

1. **Update the Token Retrieval Method**
   ```javascript
   const { accessToken } = await requestNewToken(username, password);
   ```

2. **Implement Updated Token Validation**
   - Use the updated function to check token validity.
   - Ensure all calls that depend on token validity are updated accordingly.

3. **Check User Roles and Permissions**
   ```bash
   # If applicable, run the following to reset user roles
   npm run reset-roles
   ```

**Expected Result:**
Users should be able to log in successfully, access resources, and utilize refreshed tokens without failures.

**Verification:**
```bash
curl -X GET "https://api.yourdomain.com/endpoint" -H "Authorization: Bearer <accessToken>"
```
Ensure the response returns the expected resource without errors.

---

**Method 2: Token Refresh Flow Reset** *(If primary method is not feasible)*

1. **Force Token Expiration Reset**
   - Instruct users to log out and log back in. This will issue a new token under the new rules.
   - Educate users on how to manage sessions appropriately, such as logging out when done.

**Note:** This method is reactive and not recommended for long-term fixes.

---

## Workarounds

If the above fix cannot be applied immediately:

**Temporary Solution:**
- Inform users to clear their authentication cookies and attempt re-login.
- Provide a temporary guest login option until normal authentication is restored.

**Note:** This is a temporary measure. Apply the full resolution when possible.

---

## Prevention & Best Practices

To avoid this issue in the future:

1. **Regular Dependency Reviews**
   - Ensure authentication libraries are kept up-to-date with latest security protocols.
   - Conduct audits of authentication flows on major updates.

2. **Comprehensive Testing Before Deployment**
   - Include tests that check how token expiration and validation affect user access.
   - Employ user roles and permissions testing as part of the CI/CD pipeline.

3. **User Education & Documentation**
   - Keep users informed of any upcoming changes, particularly those relating to authentication, via internal communications.
   - Maintain up-to-date documentation on authentication processes.

---

## References

- **Related GitHub Issues:** [#3719](https://bigcommercecloud.atlassian.net/browse/b2b-3719), [#582](https://bigcommercecloud.atlassian.net/browse/b2b-3216)
- **Pull Requests:** [PR #614](https://github.com/yourrepo/pull/614), [PR #596](https://github.com/yourrepo/pull/596)
- **Documentation:** [Authentication Overview](https://yourcompany.com/docs/authentication)
- **Related Articles:** [Managing User Roles](https://yourcompany.com/docs/user_roles)

---

## Related Articles

- [Understanding API Token Expiration](https://yourcompany.com/docs/token_expiration)
- [Troubleshooting Authentication Issues](https://yourcompany.com/docs/auth_troubleshooting)
- [Configuration Changes & Their Effects](https://yourcompany.com/docs/config_changes)

---

## Support Escalation

If this article does not resolve your issue:

1. **Gather the following information:**
   - Full error message and stack trace
   - Environment details (OS, versions, configurations)
   - Steps to reproduce the issue
   - Logs from: `/var/logs/auth.log`

2. **Contact Support:**
   - Submit via: [Support channel](https://yourcompany.com/support)
   - Include: All information from step 1
   - Reference: This KB article

---

*Last Updated: October 3, 2023*
*Article ID: B2B-Buyer-Portal-Auth-001*