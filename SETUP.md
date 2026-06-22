# 🛠️ Setup — Turn On the Agent (5 minutes)

Do this **once** before the challenges. All point-and-click in the AWS Console.

---

## ⚠️ First: the money bit (30 seconds to read)

**AWS DevOps Agent is a paid service.** It is **not** free-tier. New users get a **2-month free trial** — use that for this sprint. To be safe, set a budget alert (below) and **delete your stacks** after each challenge.

---

## Step 1: Set a budget alert (safety net)

1. In the AWS Console search bar, type **Budgets** and open it.
2. Click **Create budget** → **Use a template** → **Monthly cost budget**.
3. Set the amount to **$5** and enter your email.
4. Create it. Now AWS emails you if spend crosses **$5**. Done.

---

## Step 2: Open AWS DevOps Agent

1. In the Console search bar, type **DevOps Agent** and open it.
2. If it asks you to **start the free trial / enable** the service, do it.
3. If the page says it's not available in your Region, switch your Region (top-right corner) to one that's supported, then reopen.

---

## Step 3: Create an Agent Space

An **Agent Space** is just the agent's workspace — it decides which of your AWS stuff the agent can look at.

1. Click **Create Space** (or **Create Agent Space**).
2. Name it `bss-may-2026`.
3. Connect **your AWS account** with **read-only** access (the default is fine).
4. Save.

That's the whole setup. You'll reuse this same Space for every challenge.

---

## The agent has two screens

- **Console (admin):** where you created the Space.
- **DevOps Agent web app (operator):** where you **chat** and read **investigations**. This is where you'll spend the challenges.

---

## ✅ Ready?

- [ ] Free trial / service enabled
- [ ] Budget alert set ($5)
- [ ] Agent Space `bss-may-2026` created

Go to **[challenge-1-meet-your-agent](challenge-1-meet-your-agent/README.md)**.
