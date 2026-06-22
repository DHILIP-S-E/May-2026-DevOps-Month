# Solution — Challenge 3: Stress & Diagnose

No solution is published for this challenge — that's intentional.

This is a **performance** investigation, not an obvious bug. Let **AWS DevOps Agent** diagnose what's overloading the instance, then act on its recommendation and watch the alarm recover.

If you're stuck:
- Wait ~2 minutes after the stack finishes so the `challenge3-high-cpu` alarm fires.
- Point the agent at the alarm name to give it a starting point.
- Connect via **Session Manager** and use tools like `top` to see what the agent is pointing at.

Facilitators hold the answer key.
