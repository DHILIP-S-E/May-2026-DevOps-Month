# Challenge 3: Stress & Diagnose ⭐⭐⭐ Hard

## The Scenario
A small EC2 server (`challenge3-stress`) has become **painfully slow**. Users are complaining, dashboards look unhappy, and an alarm has gone red — but nobody knows why. The server isn't misconfigured in any obvious way; something is *running* on it.

Your job: use **AWS DevOps Agent** to diagnose the slowdown, identify what's eating the resources, and bring the server back to health. The README won't tell you the cause — investigate and find it.

## Time
~25 minutes. 💰 EC2 costs by the hour — **delete the stack as soon as you finish.**

## Before You Start
Finish [SETUP.md](../SETUP.md).

> 🔎 **The agent can only investigate what it can see.** Deploy this challenge into the **same AWS account and Region your Agent Space monitors** (these steps use `us-east-1`, where DevOps Agent runs). After the stack is created, give the agent a few minutes to discover the new resources.

## Steps

### 1. Create the infrastructure

**Option A — AWS CLI (fastest).** From this challenge's folder, run:
```bash
aws cloudformation deploy \
  --stack-name challenge-3 \
  --template-file template.yaml \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```
Wait for `Successfully created/updated stack - challenge-3`.

**Option B — Console (point-and-click).**
1. Console search → **CloudFormation** → **Create stack** → **With new resources (standard)**.
   > ⚠️ Pick **"standard"**, NOT "With existing resources (import resources)" — the import option throws a `DeletionPolicy` error.
2. **Choose an existing template** → **Upload a template file** → **Choose file**.
3. Upload this file:
   ```
   May-2026/challenge-3-stress-and-diagnose/template.yaml
   ```
4. **Next** → Stack name: `challenge-3` → **Next** → **Next**.
5. Tick **"I acknowledge… IAM resources"** → **Submit**. Wait for **CREATE_COMPLETE** (~2 min).

> The server starts misbehaving on its own the moment it boots — you don't need to break anything. Give it ~2 minutes, then the alarm `challenge3-high-cpu` will go red.

### 2. Investigate with the agent
1. Open the **DevOps Agent** web app → **Chat**.
2. Ask it to investigate the slow server, for example:
   ```
   My EC2 instance challenge3-stress is slow and its alarm is firing.
   Investigate the cause and tell me how to fix it.
   ```
3. Read the agent's diagnosis. Work out *what* is overloading the instance and *what it recommends*.

### 3. Apply the fix (required)
1. Connect to the server to act on the agent's findings: Console → **EC2** → **Instances** → `challenge3-stress` → **Connect** → **Session Manager** tab → **Connect**.
   > Session Manager greyed out? Wait ~1 minute after the stack finishes — the instance needs a moment to register.
2. Use what the agent found to bring CPU back to normal (for example, stop the runaway work it identified).
3. Watch the alarm `challenge3-high-cpu` return to green, then ask the agent to confirm the instance has recovered.

## ✅ You're done when…
- The agent has identified what's overloading the instance, **and**
- You've acted on its recommendation so CPU drops and the alarm clears.

## 📸 Submit
Create a file **`FINDINGS.md`** in this folder (`challenge-3-stress-and-diagnose/`) using this template:

```markdown
# Challenge 3 — Findings

## Root cause
(what the agent found was overloading the instance — in your own words)

## Fix applied
(what you did to bring CPU back to normal)

## Evidence
- [ ] Screenshot 1: the agent's diagnosis
- [ ] Screenshot 2: recovery — the `challenge3-high-cpu` alarm back to green
```

Then submit it at [awsugmdu.in](https://www.awsugmdu.in/), with both screenshots attached.

## Hints
- Alarm not red yet? Wait ~2 minutes — it checks every minute. Watch it: Console → **CloudWatch** → **Alarms** → `challenge3-high-cpu`.
- Give the agent a starting point by mentioning the alarm name `challenge3-high-cpu`.
- Inside Session Manager, tools like `top` can help you see what the agent is pointing at.

## Bonus Points
- Ask the agent: "how do I stop this from happening again?" and note what it suggests (right-sizing, Auto Scaling, etc.).

## ⚠️ Destroy the infrastructure (cleanup — important, this one costs money)

Do this the moment you finish — EC2 bills by the hour.

**Option A — AWS CLI:**
```bash
aws cloudformation delete-stack --stack-name challenge-3 --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name challenge-3 --region us-east-1
```

**Option B — Console:** CloudFormation → `challenge-3` → **Delete** → confirm, wait for **DELETE_COMPLETE**.

Then confirm **EC2 → Instances** shows the instance terminated. See [COST-AND-CLEANUP.md](../COST-AND-CLEANUP.md).
