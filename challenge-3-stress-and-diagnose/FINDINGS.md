# Challenge 3 — Findings: Stress & Diagnose

**Submitted by:** [Your name]  
**Date:** [Date]

---

## What I deployed

CloudFormation stack `challenge-3` from `template.yaml`.  
Resource created: EC2 instance that automatically stresses its own CPU on boot.

---

## How the failure appeared

Waited ~2 minutes after stack creation.  
Alarm `challenge3-high-cpu` turned **red** automatically (CPU spike triggered by the instance itself).

---

## What the agent found

Asked DevOps Agent:
> "challenge3-stress EC2 alarm is firing. Diagnose what's killing CPU and how to fix."

**Root cause identified:**  
[Paste the agent's diagnosis here — e.g. "A stress process (`stress` or `yes > /dev/null`) is consuming 100% CPU. It was launched by a startup script in user data."]

**Process identified:**  
[e.g. `stress --cpu 2` or `yes > /dev/null &`]

---

## Fix applied

1. EC2 → instance → **Connect** → **Session Manager** → Connect
2. Ran `top` to confirm the process was consuming CPU
3. Killed the process:
   ```
   kill <PID>
   ```
   or
   ```
   pkill stress
   ```
4. Confirmed CPU dropped back to normal with `top`

Alarm `challenge3-high-cpu` returned to **green** within ~1 minute.

---

## After fix

EC2 CPU: back to normal  
Alarm `challenge3-high-cpu`: **green** (OK)

---

## Evidence

- [ ] Screenshot 1: Agent diagnosis (identifying the process killing CPU)
- [ ] Screenshot 2: Alarm green after the process was killed

---

## Cleanup

Stack `challenge-3` deleted immediately after completion — EC2 bills by the hour.
