# Challenge 4 — Findings: Bad Deploy Detective

**Submitted by:** [Your name]  
**Date:** [Date]

---

## What I deployed

CloudFormation stack `challenge-4` from `template.yaml`.  
Resources created: Lambda `challenge4-app-fn`, DynamoDB table `challenge4-data`, seeded with one product item.

---

## How I reproduced the failure

Lambda → `challenge4-app-fn` → Test tab → clicked Test 4 times.  
All 4 invocations failed. Alarm `challenge4-app-fn-errors` turned red.

Opened the Code tab — the code looked completely correct (reading from DynamoDB, returning JSON). No obvious bug.

---

## What the agent found

Asked DevOps Agent:
> "challenge4-app-fn fails after deploy but code looks correct. Find the real root cause."

**Root cause identified:**  
The Lambda's IAM execution role (`challenge4-app-fn` role) is missing the `dynamodb:GetItem` permission for the `challenge4-data` table. The "bad deploy" removed the DynamoDB read policy from the role. The code is fine — it cannot execute because the role has no permission to read from the table.

**Error in CloudWatch Logs:**  
`AccessDeniedException: User: arn:aws:sts::... is not authorized to perform: dynamodb:GetItem on resource: ...challenge4-data`

---

## Fix applied

No code change — the code was correct.

Fixed the IAM role:

Option A (Console):
1. IAM → Roles → find the role attached to `challenge4-app-fn`
2. Add inline policy or attach managed policy granting `dynamodb:GetItem` on `challenge4-data` ARN
3. Save

Option B (Lambda Console):
1. Lambda → `challenge4-app-fn` → Configuration → Permissions
2. Click the execution role link → IAM opens
3. Add permission: `dynamodb:GetItem` on the table ARN

---

## After fix

Clicked Test — invocation **succeeded**.  
Response:
```json
{"statusCode": 200, "body": "{\"item\": {\"id\": {\"S\": \"1\"}, \"product\": {\"S\": \"Builders Hoodie\"}, \"price\": {\"N\": \"49\"}}}"}
```

Alarm `challenge4-app-fn-errors` returned to **green** (OK).

---

## Evidence

- [ ] Screenshot 1: Agent root-cause finding (identifying the missing IAM permission)
- [ ] Screenshot 2: Successful test result showing product data
- [ ] Screenshot 3: Alarm green after fix
