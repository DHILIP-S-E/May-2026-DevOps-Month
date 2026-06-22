# 🤖 Builders Skill Sprint — May 2026

## Meet AWS DevOps Agent (No-Code-Required Edition)

Welcome! In this hands-on set you'll try **AWS DevOps Agent** — an AI teammate that looks at your AWS resources, finds what's wrong, and tells you how to fix it. **No CDK and no coding needed — console-only, with optional one-line CLI commands if you prefer the terminal.** You'll be handed a broken AWS scenario, then **ask the agent in plain English** to investigate, find the root cause, and guide you to the fix.

> ⚠️ **AWS DevOps Agent is a paid service** (with a 2-month free trial — *not* free-tier). Read [COST-AND-CLEANUP.md](COST-AND-CLEANUP.md) **before you start** and delete your stacks when done.

---

## 🎯 Challenges

| # | Challenge | What you do | Difficulty |
|---|-----------|-------------|-----------|
| 1 | **Meet Your Agent** | Create the agent and chat with it | ⭐ Easy |
| 2 | **First Investigation** | A Lambda fails every run — find out why and fix it | ⭐⭐ Medium |
| 3 | **Stress & Diagnose** | A server is overloaded — diagnose it and recover it | ⭐⭐⭐ Hard |
| 4 | **Bad Deploy Detective** | An app fails after a deploy — find the hidden cause | ⭐⭐⭐⭐ Expert |
| 5 | **Build Your Own** | Invent your own break-and-investigate | 🚀 Innovate |

Every challenge is **point-and-click in the AWS Console**. You deploy a broken scenario, then investigate it with the agent — the hardest thing you'll type is a question.

---

## 📋 What You Need

- An **AWS account** with **AWS DevOps Agent** turned on (start the free trial)
- A web browser — that's it
- About **20–30 minutes** per challenge

Everything works **console-only** (point-and-click). Each challenge also gives you optional **AWS CLI** commands to create and destroy its infra if you prefer the terminal — but no CDK, SAM, or GitHub setup is ever required.

---

## 🚀 Start Here

1. Read **[SETUP.md](SETUP.md)** once — it shows how to turn on the agent (5 minutes).
2. Do the challenges in order (1 → 5). Each folder has a `README.md` with step-by-step clicks.
3. There are **no published solutions** — the agent is your guide. Investigate with it; facilitators hold the answer key.

---

## 📁 Folder Structure

```
May-2026/
├── README.md                   ← You are here
├── SETUP.md                    ← Turn on the agent (do this first)
├── COST-AND-CLEANUP.md         ← How to avoid charges (read it!)
├── challenge-1-meet-your-agent/
├── challenge-2-first-investigation/
├── challenge-3-stress-and-diagnose/
├── challenge-4-bad-deploy-detective/
└── challenge-5-build-your-own-agentic-sre/
```

Each challenge folder has:
- `README.md` — the scenario and the steps
- `template.yaml` — the file you upload in the console (challenges **2–4**; challenge 1 is chat-only, challenge 5 is build-your-own)
- `solution/` — a short note only (no answers — investigate with the agent)

---

## 🏆 Scoring

| # | Challenge | Difficulty |
|---|-----------|------------|
| 1 | Meet Your Agent | ⭐ |
| 2 | First Investigation | ⭐⭐ |
| 3 | Stress & Diagnose | ⭐⭐⭐ |
| 4 | Bad Deploy Detective | ⭐⭐⭐⭐ |
| 5 | Build Your Own | 🚀 Best one gets a shoutout! |

---

## 📝 How to Submit

For each challenge, create a **`FINDINGS.md`** file inside that challenge's folder and fill it in. Each challenge README has a ready-to-copy template — it asks for three things:

1. **Root cause** — what the agent found, in your own words.
2. **Fix applied** — what you changed to resolve it (Challenge 1 has no fix — it's just a chat intro).
3. **Evidence** — two screenshots: the agent's root-cause finding, and proof of recovery (the alarm back to green, or a healthy response).

Then submit at [https://www.awsugmdu.in/](https://www.awsugmdu.in/) — say which challenge, and attach your `FINDINGS.md` and screenshots.

---

## 💡 Tips

- Start with Challenge 1 — it proves your free trial works before you spend anything.
- Always **delete your stack** after each challenge (one button in CloudFormation).
- The agent understands plain English — just ask it like you'd ask a coworker.

---

## 🔗 Resources

- [AWS DevOps Agent product page](https://aws.amazon.com/devops-agent/) — features + free trial
- [About AWS DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html)
- [Getting Started](https://docs.aws.amazon.com/devopsagent/latest/userguide/getting-started-with-aws-devops-agent.html)

---

Happy investigating! 🚀
