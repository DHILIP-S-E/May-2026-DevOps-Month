# Challenge 2 — Findings: First Investigation

**Submitted by:** [Your name]  
**Date:** [Date]

---

## What I deployed

CloudFormation stack `challenge-2` from `template.yaml`.  
Resource created: Lambda function `challenge2-broken-fn`.

---

## How I reproduced the failure

Lambda → `challenge2-broken-fn` → Test tab → clicked Test 4 times.  
All 4 invocations failed. Alarm `challenge2-broken-fn-errors` turned red.

---

## What the agent found

Asked DevOps Agent:
> "challenge2-broken-fn fails every run. Find root cause and fix."

**Root cause identified:**  
[Paste the agent's finding here — e.g. "NameError: config is not defined on line X" or "missing environment variable" etc.]

---

## Fix applied

[Describe what you changed — e.g. "Fixed the NameError in the Lambda code by removing the reference to the undefined `config` variable and returning a hardcoded response" or "Added missing environment variable TABLE_NAME"]

Steps taken:
1. Lambda → `challenge2-broken-fn` → Code tab
2. [Describe the edit]
3. Clicked **Deploy**

---

## After fix

Clicked Test — invocation **succeeded**.  
Alarm `challenge2-broken-fn-errors` returned to **green** (OK).

---

## Evidence

- [ ] Screenshot 1: Agent root-cause finding
- [ ] Screenshot 2: Alarm green after fix (or successful test result)
