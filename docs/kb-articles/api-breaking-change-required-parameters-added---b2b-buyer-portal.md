# API Breaking Change: Required Parameters Added - b2b-buyer-portal

# API Breaking Change: Required Parameters Added

> **Problem Statement:** Users may encounter 422 Validation errors due to newly added required parameters in API endpoints.

---

## Applies To

- **Product/Repository:** b2b-buyer-portal
- **Language/Framework:** None
- **Versions:** All versions
- **Environment:** Both Development and Production

---

## Symptoms

Users experiencing this issue may observe:

- **Error Messages:**
  ```
  422 Unprocessable Entity: Required parameter 'category_id' is missing.
  422 Unprocessable Entity: Required parameter 'order_id' is missing.
  ```

- **Behavior:**
  - Users receive validation errors upon making API requests that do not include newly required parameters.
  - API responses fail with a 422 status code, indicating issues with the input data.

- **Impact:**
  - Users may be unable to complete API requests necessary for their applications, leading to disruptions in their workflow.

---

## Root Cause

### Technical Explanation

This issue arises from recent updates to the API that introduced new required parameters for various endpoints. As a result, any requests lacking these parameters will now trigger validation errors.

**Key factors:**
- Added required parameters for key endpoints to enhance data integrity and processing.
- Changes in validation rules leading to stricter enforcement of input requirements.
- Updates that were rolled out without backward compatibility for existing integrations.

---

## Resolution

### Migration Guide (For Code-Level Changes)

**IMPORTANT:** If this issue is due to code changes (API updates), please ensure to follow the migration guide below:

#### Before (Old Code/Config):
```json
{
  "item_id": "12345",
  "quantity": 2
}
```

#### After (New Code/Config):
```json
{
  "item_id": "12345",
  "quantity": 2,
  "category_id": "electronics",  // Newly required parameter
  "order_id": "98765"            // Newly required parameter
}
```

#### What Changed:
- Added `category_id` as a required parameter.
- Added `order_id` as a required parameter.

---

### Step-by-Step Fix

**Method 1: Update API Request Payload**

Follow these steps to resolve the issue:

1. **Identify Required Parameters:**
   Make sure to include all required parameters in your API requests:
   ```json
   {
     "item_id": "12345",
     "quantity": 2,
     "category_id": "electronics",  // Ensure this added parameter exists
     "order_id": "98765"            // Ensure this added parameter exists
   }
   ```

2. **Modify Your API Calls:**
   - Update your existing API calls to include the required parameters.
   - Ensure the new format adheres to the API documentation.

3. **Test Your API Call:**
   ```bash
   curl -X POST https://api.example.com/orders \
   -H "Content-Type: application/json" \
   -d '{
         "item_id": "12345",
         "quantity": 2,
         "category_id": "electronics",
         "order_id": "98765"
       }'
   ```

**Expected Result:**
The API should return a success status (2XX code) indicating that the request was processed correctly.

---

## Additional Recommendations:

- **Check API Documentation:** Ensure your team reviews the latest API documentation to understand fully all recent changes.
- **Test in Staging:** Before deploying to production, verify your changes in a staging environment to avoid workflow disruptions.
- **Subscribe to Changes:** Consider subscribing to the API change log or alerts to receive notifications of future updates.

---

For further assistance, please visit our [support page](#).
```

This revised content adds necessary completeness, improves clarity, and ensures readiness for customer-facing documentation.