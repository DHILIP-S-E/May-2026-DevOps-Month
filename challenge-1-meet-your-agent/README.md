# Challenge 1: Meet Your Agent ⭐

## Goal
Say hello to AWS DevOps Agent. You'll **chat** with it and ask about your AWS account — no breaking anything yet. This proves your free trial works.

## Time
~10 minutes. Costs almost nothing.

## Before You Start
Finish [SETUP.md](../SETUP.md) — you should have an Agent Space called `bss-may-2026`.

## Steps

1. Open the **AWS DevOps Agent** web app (the operator screen from setup).
2. Find the **Chat** box (usually "Chat with DevOps Agent").
3. Ask it these, one at a time:
   - `What resources do I have in this account?`
   - `Is anything unhealthy right now?`
   - `Give me a health summary of my environment.`
4. Read its answers. Notice it talks like a person, not a dashboard.

> **Empty account with nothing to show?** Deploy Challenge 2's tiny Lambda first so the agent has something to look at:
> 1. Console → **CloudFormation** → **Create stack** → **With new resources (standard)** *(not "import")*.
> 2. **Upload a template file** → upload `May-2026/challenge-2-first-investigation/template.yaml`.
> 3. Name it `challenge-2`, acknowledge IAM, **Submit**, wait for **CREATE_COMPLETE**.
> Then give the agent a few minutes to discover it and ask again. (Otherwise just use whatever's already in your account.)
>
> Make sure the resources are in the **same account and Region your Agent Space monitors** — these challenges use `us-east-1`, where DevOps Agent runs.

## ✅ You're done when…
The agent gives you a plain-English answer about your account (even "you have no resources" counts — it answered!).

## 📸 Submit
Create a file **`FINDINGS.md`** in this folder (`challenge-1-meet-your-agent/`) using this template:

```markdown
# Challenge 1 — Findings

## What I asked the agent
(the questions you tried)

## What the agent told me
(its plain-English answer about your account, in your own words)

## Evidence
- [ ] Screenshot: the agent's reply
```

Then submit it at [awsugmdu.in](https://www.awsugmdu.in/), with your screenshot attached.

## Hints
- Don't see Chat? Make sure you're in the **DevOps Agent web app**, not the admin Console page.
- The agent only sees what your Agent Space is connected to. If it sees nothing, recheck the account connection in setup.

## Bonus Points
- Ask a follow-up question and see if it remembers the conversation (e.g. "and which region are they in?").
- Find the **Topology** view and screenshot the map of your resources.

## ⚠️ Cleanup
Nothing to delete here. Keep your Agent Space — every challenge uses it.
