# Solution — Challenge 4: Bad Deploy Detective

No solution is published for this challenge — that's intentional.

This is the hardest one: the function's code is correct, so the root cause lives **somewhere other than the code**. Let **AWS DevOps Agent** investigate the function and everything it depends on, then apply the fix it recommends (it isn't a code change) and confirm recovery.

If you're stuck:
- Read the actual error message: Lambda → **Monitor** tab → **View CloudWatch logs**.
- Ask yourself what a function needs *besides* working code to do its job.
- Point the agent at the function, the alarm `challenge4-app-fn-errors`, and the resources the function talks to.

Facilitators hold the answer key.
