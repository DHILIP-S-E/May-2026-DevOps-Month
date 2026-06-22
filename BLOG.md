# How I Used AWS DevOps Agent to Investigate 5 Real-World Cloud Failures (No Code Required)

*AWS User Group MDU — Builders Skill Sprint, May 2026*

---

When someone says "DevOps," most people picture pipelines, YAML files, and Terraform. But what if you could investigate broken AWS infrastructure using plain English — no CLI, no code, just a conversation?

That's exactly what I did during the **May 2026 Builders Skill Sprint**, where I worked through 5 hands-on challenges using **AWS DevOps Agent** — an AI-powered teammate that reads your AWS environment, spots what's wrong, and tells you how to fix it.

Here's what I learned from each one.

---

## What Is AWS DevOps Agent?

AWS DevOps Agent is a service that connects to your AWS account and gives you a conversational interface to investigate your infrastructure. You ask it questions like you'd ask a senior SRE colleague:

> "My Lambda is failing every run. What's wrong?"

It looks at your CloudWatch logs, IAM roles, Lambda configs, EC2 metrics — and tells you exactly what it found.

No dashboards to click through. No log queries to write. Just answers.

---

## Challenge 1 — Meet Your Agent ⭐

**Time:** 10 minutes | **Cost:** Free

The first challenge was simple: open the agent and start a conversation. I asked three questions:

- *"What resources do I have in this account?"*
- *"Is anything unhealthy right now?"*
- *"Give me a health summary"*

The agent scanned my account and gave me a clear picture of what was running and what to watch out for. No infra to deploy, no cost — just proof that the free trial was working and that the agent actually understands your account.

**What I learned:** The agent reads your AWS environment in real time. It's not pulling from a cache — it's looking at your actual resources.

---

## Challenge 2 — First Investigation ⭐⭐

**Time:** 20 minutes | **Cost:** Minimal (Lambda only)

I deployed a CloudFormation stack that created one Lambda function: `challenge2-broken-fn`. I ran it four times. It failed four times. The CloudWatch alarm turned red.

Then I opened DevOps Agent and typed:

> "challenge2-broken-fn fails every run. Find root cause and fix."

The agent pulled the CloudWatch logs, read the stack trace, and identified the exact line causing the crash. It wasn't a mystery — the code referenced a variable that didn't exist.

I fixed it in the Lambda Code tab, hit Deploy, ran the test again — and it passed. Alarm went green.

**What I learned:** The agent reads CloudWatch logs so you don't have to dig through them manually. It surfaces the root cause in plain English.

---

## Challenge 3 — Stress & Diagnose ⭐⭐⭐

**Time:** 25 minutes | **Cost:** Small (EC2 — delete immediately after!)**

This one was more interesting. I deployed a stack that created an EC2 instance. Within two minutes, the `challenge3-high-cpu` alarm turned red — the instance was using 100% CPU, and I hadn't done anything.

I asked DevOps Agent:

> "challenge3-stress EC2 alarm is firing. Diagnose what's killing CPU and how to fix."

It identified the exact process causing the spike — a stress tool that had been deliberately started by the instance's startup script. It told me the process name and how to kill it.

I connected to the instance via Session Manager (no SSH keys needed), ran `top` to confirm, killed the process, and watched the alarm go back to green.

**What I learned:** The agent doesn't just read metrics — it reasons about what caused them. It connected the alarm to the running process to the startup script.

> ⚠️ This challenge uses EC2 which bills by the hour. Delete the stack as soon as you're done.

---

## Challenge 4 — Bad Deploy Detective ⭐⭐⭐⭐

**Time:** 25 minutes | **Cost:** Minimal (Lambda + DynamoDB)

This was the trickiest one — and the most realistic.

I deployed a stack, ran the Lambda four times, and it failed every time. But when I opened the Code tab, everything looked completely correct. The code was clean, the logic was sound, there were no obvious bugs.

The trick? The problem wasn't in the code at all.

I asked DevOps Agent:

> "challenge4-app-fn fails after deploy but code looks correct. Find the real root cause."

The agent found it: the Lambda's IAM execution role was missing `dynamodb:GetItem` permission on the table it needed to read. A "bad deploy" had silently removed that permission. The code never had a chance to run correctly.

I added the permission — no code change at all — and the Lambda immediately returned the correct product data. Alarm cleared.

**What I learned:** Not every bug is a code bug. The agent looks beyond the function itself — at IAM roles, permissions, and configurations — and finds the real cause even when the code looks fine.

---

## Challenge 5 — The Phantom Deploy 🚀

**Time:** 45 minutes | **Cost:** Minimal (API GW + Lambda + DynamoDB)

This is the challenge I built myself — and the one I'm most proud of.

### The scenario

I wanted to create a failure that was harder than a crash. Instead of making something break with an error, I made something break **silently**.

The setup:
- An API Gateway backed by a Lambda
- The Lambda reads from a DynamoDB table and returns product data
- **Two** DynamoDB tables exist: `phantom-real-data` (correct data) and `phantom-shadow-data` (empty)
- After a "deploy," the Lambda's `TABLE_NAME` environment variable was pointing to the wrong table

The result? Every API call returned **HTTP 200 OK**. No Lambda errors. No CloudWatch alarms firing. Everything looked perfectly healthy.

But the data was always wrong:
```json
{"product": "unknown", "price": "0", "status": "ok"}
```

### Why this is harder than a crash

Standard monitoring catches service failures. If a Lambda throws an exception, an alarm fires. If a server goes down, a health check fails.

But this failure had **nothing to alert on**:

| Check | Status |
|-------|--------|
| Lambda error count | 0 ✅ |
| API Gateway 5xx | 0 ✅ |
| Latency alarm | Green ✅ |
| CloudWatch alarms | All green ✅ |

The service was healthy. The data was wrong. Zero alerts.

### The investigation

I asked DevOps Agent:

> "My API (phantom-deploy-api) returns HTTP 200 OK on every call but the data is always wrong — product shows 'unknown' and price shows '0'. No Lambda errors. No alarms firing. Find the root cause."

The agent had to correlate three things:
1. The Lambda's `TABLE_NAME` environment variable value
2. The contents of the table it was actually reading from
3. The contents of the other table that had the correct data

It found the mismatch: `TABLE_NAME` was set to `phantom-shadow-data` (empty), not `phantom-real-data` (which had the correct product).

### The fix

Lambda → Configuration → Environment variables → Edit.

Changed `TABLE_NAME` from `phantom-shadow-data` to `phantom-real-data`.

Saved. Called the API again:
```json
{"product": "Builders Hoodie", "price": "49", "status": "ok"}
```

The phantom was gone.

### The SKILL.md bonus

I also wrote a custom **runbook** (SKILL.md) — a markdown document that tells the agent exactly how to investigate "200 OK but wrong data" failures step by step. When I uploaded it as a Skill in the agent space, it followed my runbook during the investigation.

Writing your own runbook and watching the agent follow it is a completely different experience from just asking it questions.

---

## Key Takeaways

**1. The agent understands context, not just metrics.**
It doesn't just tell you "CPU is high" — it tells you which process, why, and how to stop it.

**2. Silent failures are the hardest class of problem.**
A crash is easy to detect. Wrong data with no errors is invisible to standard monitoring. That's where the agent's ability to correlate configuration + data becomes most valuable.

**3. IAM is always the hidden culprit.**
Challenge 4 taught me to check IAM permissions before assuming a code bug. The code was fine. The role wasn't.

**4. You can teach the agent your runbooks.**
The SKILL.md bonus in Challenge 5 was the most underrated feature. You write a procedure once, and the agent follows it every time.

**5. Deletion is part of the challenge.**
Three of the five challenges create billable resources. The discipline of deploying fast, investigating fast, and deleting immediately is a real DevOps skill.

---

## What's Next

These challenges are available at the **AWS User Group MDU Builders Skill Sprint** at [awsugmdu.in](https://www.awsugmdu.in/).

If you want to try "The Phantom Deploy" scenario yourself, the full CloudFormation template, runbook, and walkthrough are in the [GitHub repo](https://github.com/DHILIP-S-E/May-2026-DevOps-Month/tree/main/challenge-5-build-your-own-agentic-sre).

The hardest part isn't the technology. It's learning to ask the right question.

---

*Written by Dhilip S E | AWS User Group MDU | May 2026*
