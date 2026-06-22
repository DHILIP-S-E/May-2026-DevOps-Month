# ⚠️ Cost & Cleanup — Don't Skip This

AWS DevOps Agent is **paid** (2-month free trial, not free-tier). The challenges also create small AWS resources. None of it costs much, but **leftover resources cost money**, so always clean up.

---

## The 3 rules

1. **Use the free trial** for the agent.
2. **Set a $5 budget alert** (see [SETUP.md](SETUP.md)).
3. **Delete every stack** the moment you finish a challenge.

---

## How to delete a stack

Challenges **2–4** create a **CloudFormation stack**. To remove everything that challenge made:

**Option A — AWS CLI** (replace `challenge-2` with the stack you want gone):
```bash
aws cloudformation delete-stack --stack-name challenge-2 --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name challenge-2 --region us-east-1
```

**Option B — Console (point-and-click):**
1. In the Console search bar, type **CloudFormation** and open it.
2. Click your stack (e.g. `challenge-2`).
3. Click **Delete** → confirm.
4. Wait for status **DELETE_COMPLETE**. Everything that challenge created is now gone.

That's it — one delete removes the whole challenge.

---

## At the end of the sprint

- Delete **all** challenge stacks (check the CloudFormation list is empty).
- In **DevOps Agent**, delete your Agent Space `bss-may-2026`.

---

## ✅ "Did I clean up?" checklist

- [ ] No challenge stacks left in CloudFormation
- [ ] EC2 instance from Challenge 3 is gone (search **EC2** → Instances)
- [ ] Challenge 4's DynamoDB table `challenge4-data` is gone (deleting the stack removes it)
- [ ] Agent Space deleted
- [ ] Budget shows no surprise spend

> 💰 **Challenge 3 (EC2) is the priciest** — delete that stack first. Everything else is tiny, but always delete each stack when you finish the challenge.
