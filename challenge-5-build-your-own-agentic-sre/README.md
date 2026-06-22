# Challenge 5 (Innovate): The Phantom Deploy 🚀

## The Scenario

A developer deployed a new version of the product API. The deploy **succeeded**.

- CloudFormation status: `CREATE_COMPLETE`
- Lambda errors: **0**
- API Gateway 5xx errors: **0**
- All CloudWatch alarms: **GREEN**
- Every API call returns: **HTTP 200 OK**

But users are complaining: *"The product page always shows 'unknown' with price $0."*

**Zero errors. Zero alerts. Zero clues. Just wrong answers.**

This is harder to diagnose than a crash — there's nothing to page on.

---

## Architecture

```
User → API Gateway (phantom-deploy-api)
           → GET /product
           → Lambda (phantom-api-fn)
               → DynamoDB reads TABLE_NAME env var
               → Returns 200 OK always

Two DynamoDB tables exist:
  phantom-real-data   ← has {"product":"Builders Hoodie","price":"49"}
  phantom-shadow-data ← empty (no items)

The Lambda reads from TABLE_NAME.
After the deploy, TABLE_NAME points to the wrong table.
```

---

## What You Deploy

| Resource | Name |
|----------|------|
| API Gateway | `phantom-deploy-api` |
| Lambda | `phantom-api-fn` |
| Real table (correct data) | `phantom-real-data` |
| Shadow table (empty) | `phantom-shadow-data` |
| Error alarm (stays green) | `phantom-fn-errors` |
| Latency alarm (stays green) | `phantom-api-latency` |

---

## Steps

### 1. Deploy

CloudFormation → **Create stack** → Upload `template.yaml` from this folder  
Stack name: `challenge-5` → tick the IAM box → **Submit**  
Wait for `CREATE_COMPLETE` (~2 minutes).

### 2. Reproduce the problem

Copy the `ApiEndpoint` URL from the CloudFormation **Outputs** tab.  
Open it in your browser or run:

```
curl <ApiEndpoint>
```

You'll get:
```json
{"product": "unknown", "price": "0", "status": "ok"}
```

HTTP 200. No error. Wrong data.

Check CloudWatch → Alarms — everything is **green**. Nothing to alert on.

### 3. Investigate with DevOps Agent

Open **AWS DevOps Agent** → your `bss-may-2026` space → chat.

Type:
```
My API (phantom-deploy-api) returns HTTP 200 OK on every call but the data
is always wrong — product shows "unknown" and price shows "0".
No Lambda errors. No alarms firing. Find the root cause.
```

The agent will correlate:
- Lambda environment variables
- DynamoDB table contents
- What `TABLE_NAME` actually resolves to

It will find that `TABLE_NAME` points to `phantom-shadow-data` (empty) instead of `phantom-real-data` (has the data).

### 4. Fix it

Go to **Lambda → phantom-api-fn → Configuration → Environment variables → Edit**.

Change `TABLE_NAME` from `phantom-shadow-data` to `phantom-real-data`.  
Click **Save**.

### 5. Verify

Call the API again:
```json
{"product": "Builders Hoodie", "price": "49", "status": "ok"}
```

Correct data. The phantom is gone.

---

## Bonus: Use the SKILL.md runbook

Upload `SKILL.md` from this folder as a **Skill** in your DevOps Agent space.  
The agent will follow your runbook step-by-step when investigating.  
Screenshot the agent following your custom steps — bonus point on judging.

---

## Write FINDINGS.md

Create a **`FINDINGS.md`** file in this folder using the template below.

```markdown
# Challenge 5 — Findings

## What I built and how I broke it
(describe your scenario)

## What the agent found
(the root cause it identified, in your own words)

## Fix applied
(what you changed to resolve it)

## Evidence
- [ ] Screenshot: agent's root-cause finding
- [ ] Screenshot: API returning correct data after fix
- [ ] Bonus: Screenshot of agent following SKILL.md runbook
```

Then submit at [awsugmdu.in](https://www.awsugmdu.in/) with your screenshots attached.

---

## Why this is unique

| Scenario | Failure mode | Detectable by alarms? |
|----------|--------------|-----------------------|
| Silent Pipeline (already submitted) | Services crash with errors | Yes — errors fire |
| **The Phantom Deploy** | Everything healthy, wrong output | **No — nothing to alert on** |

The agent must reason about data correctness, not just service health. That's a harder class of problem.

---

## Judging

| Criteria | Weight |
|----------|--------|
| **Creativity** — original scenario | ⭐⭐⭐ |
| **It works** — agent actually investigated it | ⭐⭐⭐ |
| **Clarity** — clear write-up | ⭐⭐ |
| **Bonus** — SKILL.md runbook used | ⭐ |

---

## Cleanup

**Delete the stack when done** — CloudFormation → `challenge-5` → **Delete**.  
See [COST-AND-CLEANUP.md](../COST-AND-CLEANUP.md).

DynamoDB on-demand and API Gateway cost almost nothing, but clean up anyway.
