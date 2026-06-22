# Skill: Phantom Data — Silent Wrong-Output Investigation

## When to use this runbook

Use when an API or Lambda returns **HTTP 200 OK on every request but the response data is wrong or missing**, AND:
- No Lambda errors in CloudWatch
- No 5xx errors in API Gateway
- All CloudWatch alarms are green

This class of failure — correct status code, wrong payload — is not caught by standard health alarms. It requires correlating configuration with data.

---

## Investigation Steps

### Step 1 — Confirm the symptom

Call the API endpoint and capture the actual response body. Compare it against the expected response.

Questions to answer:
- What fields are wrong? (nulls, zeros, "unknown", empty strings?)
- Is every call wrong, or only some?
- Did this start after a deploy or config change?

### Step 2 — Check Lambda environment variables

Go to the Lambda function → **Configuration → Environment variables**.

Look for:
- `TABLE_NAME`, `BUCKET_NAME`, `QUEUE_URL`, or similar resource pointers
- Any variable that names a downstream resource
- Compare the actual value to what the code is expected to read from

**If `TABLE_NAME` (or equivalent) points to a resource other than the intended one, that is likely the root cause.**

### Step 3 — Inspect the downstream resource the Lambda is actually using

Identify the resource the env var points to.

If it is a DynamoDB table:
- Open the table → **Items** tab
- Check whether the item the Lambda queries (`prod-001` or equivalent key) exists
- Compare the contents to what the API should return

If the table is **empty** or has **stale/different items**, that confirms the wrong-resource bug.

### Step 4 — Find the correct resource

List all DynamoDB tables (or equivalent) in the account. Identify the one that has the correct data the API should serve.

Check:
- Does a table with a similar name exist (e.g., `phantom-real-data` vs `phantom-shadow-data`)?
- Does that table contain the correct item?

### Step 5 — Confirm the root cause

Root cause is confirmed when all three are true:
1. Lambda env var points to resource A
2. Resource A is empty or has wrong data
3. Resource B (the correct resource) exists and has the right data

### Step 6 — Fix

Update the Lambda environment variable to point to the correct resource:
- Lambda → Configuration → Environment variables → **Edit**
- Change the value to the correct resource name / ARN / URL
- **Save**

### Step 7 — Verify

Call the API again. The response should now contain correct data.

---

## Why alarms don't catch this

| Check | Why it stays green |
|-------|--------------------|
| Lambda error count | No exception is thrown — `get_item` on an empty table returns `{}`, not an error |
| API Gateway 5xx | Lambda returns `statusCode: 200` even when item is not found |
| API Gateway latency | Fast response (nothing to scan in the shadow table) |
| DynamoDB throttles | No reads heavy enough to throttle |

**Standard alarm coverage does not include data-correctness checks.** This class of bug requires configuration + data correlation, not metric thresholds.

---

## Prevention checklist

After any deploy that touches environment variables:

- [ ] Confirm `TABLE_NAME` (and all resource pointer env vars) resolve to the intended resource
- [ ] Run a smoke test that checks the actual response payload, not just the status code
- [ ] Add a CloudWatch custom metric or canary that asserts expected field values in the API response
- [ ] Tag all environment-specific resources clearly (`env=prod`, `env=staging`) to prevent accidental cross-env wiring
