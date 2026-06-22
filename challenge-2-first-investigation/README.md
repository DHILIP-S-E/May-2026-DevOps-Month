# Challenge 2: First Investigation ⭐⭐ Medium

## The Scenario
A teammate deployed a small Lambda function called `challenge2-broken-fn`. Ever since it went live, **every single run fails**. Nobody has had time to look at it.

Your job: deploy the stack, trigger the function so it fails, then use **AWS DevOps Agent** to work out *what is actually wrong* and how to fix it. The README won't tell you the cause — that's the whole point. Go find it.

## Time
~20 minutes. Cheap (one small Lambda).

## Before You Start
Finish [SETUP.md](../SETUP.md). Everything here works point-and-click; the AWS CLI steps are an optional shortcut.

> 🔎 **The agent can only investigate what it can see.** Deploy this challenge into the **same AWS account and Region your Agent Space monitors** (these steps use `us-east-1`, where DevOps Agent runs). After the stack is created, give the agent a few minutes to discover the new resources.

## Steps

### 1. Create the infrastructure

**Option A — AWS CLI (fastest).** From this challenge's folder, run:
```bash
aws cloudformation deploy \
  --stack-name challenge-2 \
  --template-file template.yaml \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```
Wait for `Successfully created/updated stack - challenge-2`.

**Option B — Console (point-and-click).**
1. In the Console search bar, type **CloudFormation** and open it.
2. Click **Create stack** → choose **With new resources (standard)**.
   > ⚠️ **Pick "standard", NOT "With existing resources (import resources)".** The import option causes a `DeletionPolicy` / "Cannot execute a change set" error.
3. Under **Prepare template**, choose **Choose an existing template**.
4. Under **Template source**, choose **Upload a template file** → **Choose file** and upload:
   ```
   May-2026/challenge-2-first-investigation/template.yaml
   ```
5. Click **Next**. Name the stack `challenge-2`. Click **Next**, then **Next** again.
6. Tick the box **"I acknowledge that AWS CloudFormation might create IAM resources"**.
7. Click **Submit**. Wait ~1 minute for **CREATE_COMPLETE**.

### 2. Reproduce the failure
1. Search **Lambda** in the Console, open the function `challenge2-broken-fn`.
2. Click the **Test** tab → **Test** button (any event name, defaults are fine).
3. Run it **3–4 times** so the failures register on the alarm `challenge2-broken-fn-errors`.

### 3. Investigate with the agent
1. Open the **DevOps Agent** web app → **Chat**.
2. Ask it to investigate, for example:
   ```
   The Lambda function challenge2-broken-fn is failing on every invocation.
   Investigate and tell me the root cause and how to fix it.
   ```
3. Read the agent's investigation. Figure out the root cause from what it tells you.

### 4. Apply the fix (required)
1. Use what the agent found to correct the problem in the Lambda **Code** tab.
2. Click **Deploy**, then **Test** again — it should now succeed and the alarm should return to green.
3. Ask the agent to confirm the function is healthy again.

## ✅ You're done when…
- The agent has identified the root cause, **and**
- You've applied a fix so the function runs successfully and the alarm clears.

## 📸 Submit
Create a file **`FINDINGS.md`** in this folder (`challenge-2-first-investigation/`) using this template:

```markdown
# Challenge 2 — Findings

## Root cause
(what the agent found was wrong — in your own words)

## Fix applied
(what you changed to make the function succeed)

## Evidence
- [ ] Screenshot 1: the agent's root-cause finding
- [ ] Screenshot 2: recovery — the `challenge2-broken-fn-errors` alarm green and a successful Test
```

Then submit it at [awsugmdu.in](https://www.awsugmdu.in/), with both screenshots attached.

## Hints
- No answer yet? Run the **Test** a couple more times and wait a minute — the agent needs to see the failures.
- Point the agent at the alarm `challenge2-broken-fn-errors` if it needs a starting point.
- Want to see the raw signal yourself? Lambda → **Monitor** tab → **View CloudWatch logs**.

## ⚠️ Destroy the infrastructure (cleanup)

Do this as soon as you finish.

**Option A — AWS CLI:**
```bash
aws cloudformation delete-stack --stack-name challenge-2 --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name challenge-2 --region us-east-1
```

**Option B — Console:** CloudFormation → select `challenge-2` → **Delete** → confirm, wait for **DELETE_COMPLETE**.

See [COST-AND-CLEANUP.md](../COST-AND-CLEANUP.md).
