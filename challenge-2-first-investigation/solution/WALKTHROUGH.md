# Solution — Challenge 2: First Investigation

No solution is published for this challenge — that's intentional.

The whole point is to let **AWS DevOps Agent** do the investigating. Deploy the stack, reproduce the failure, ask the agent for the root cause, then apply the fix it points you to and confirm the function is healthy again.

If you're stuck:
- Run the function's **Test** a few more times and wait a minute so the agent has failures to look at.
- Point the agent at the alarm `challenge2-broken-fn-errors`.
- Read the raw error yourself: Lambda → **Monitor** tab → **View CloudWatch logs**.

Facilitators hold the answer key.
