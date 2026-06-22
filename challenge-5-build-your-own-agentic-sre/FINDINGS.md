# Challenge 5 — Findings: The Phantom Deploy

**Submitted by:** [Your name]  
**Date:** [Date]  
**Scenario:** The Phantom Deploy

---

## What I built and how I broke it

I deployed an API that consists of:
- **API Gateway** (`phantom-deploy-api`) → GET /product
- **Lambda** (`phantom-api-fn`) that reads a product from DynamoDB and returns it as JSON
- **Two DynamoDB tables**: `phantom-real-data` (has correct product data) and `phantom-shadow-data` (empty)

The break: the Lambda's `TABLE_NAME` environment variable points to `phantom-shadow-data` (the empty table) instead of `phantom-real-data`. The Lambda code is completely correct — it reads from whatever `TABLE_NAME` says. No exception is thrown when the item is missing; it just returns `{"product": "unknown", "price": "0"}` with a 200 OK.

Result:
- Every API call returns HTTP 200 ✅
- No Lambda errors ✅
- All CloudWatch alarms stay green ✅
- But the API always returns wrong data ❌

---

## What the agent found

<!-- Fill this in after your investigation. Example below: -->

The agent correlated three things:
1. The Lambda function `phantom-api-fn` uses `TABLE_NAME` = `phantom-shadow-data`
2. The table `phantom-shadow-data` contains no items
3. The table `phantom-real-data` exists and contains the correct product (`Builders Hoodie`, price `49`)

**Root cause:** A misconfigured environment variable after deploy. The Lambda is reading from the wrong DynamoDB table. Because DynamoDB `GetItem` on a missing key returns an empty result (not an error), the Lambda silently returns stale/default data with a 200 status code.

---

## Fix applied

Went to **Lambda → phantom-api-fn → Configuration → Environment variables → Edit**.

Changed:
```
TABLE_NAME: phantom-shadow-data   →   TABLE_NAME: phantom-real-data
```

Clicked **Save**.

---

## Before fix

```json
GET /product → HTTP 200
{"product": "unknown", "price": "0", "status": "ok"}
```

## After fix

```json
GET /product → HTTP 200
{"product": "Builders Hoodie", "price": "49", "status": "ok"}
```

---

## Evidence

- [ ] Screenshot 1: Agent root-cause finding (showing it identified the wrong TABLE_NAME)
- [ ] Screenshot 2: API response after fix (correct product data returned)
- [ ] Bonus screenshot: Agent following the SKILL.md runbook steps

---

## Why this scenario is unique

Standard monitoring catches service failures (errors, latency, crashes). This scenario is a **data-correctness failure** — the service is perfectly healthy, just reading from the wrong source. No alarm can fire because nothing is broken from the infrastructure's point of view. The agent has to reason about configuration and data together, not just metrics.
