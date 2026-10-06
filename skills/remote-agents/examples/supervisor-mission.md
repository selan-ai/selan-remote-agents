# Example: Supervisor mission

A supervisor's prompt is its mission. Every run that ends in the workspace is read against it,
so the mission says both which endings wake it and what it does then.

```
Make this workspace cheaper without doing less, one agent at a time.

Only test a cheaper model in place of the strongest one. Do not try other models, and do not
lower effort.

Pick one agent: the one on the strongest model that costs the most and is not yet settled in
your memory. Work on it alone until it is settled:
1. Write in memory what "done" means for it, from its last runs.
2. Start one run of it on the cheaper model at its current effort, and wake after that run.
   Compare it with its last runs.
3. Switch it only if the work was still done for a smaller price. Then wake after its next 5
   runs, and revert if any of them did less.
4. Once it is settled, write the result in memory and pick the next agent.

After each run of the agent you are working on: if it failed, timed out, or its report says
work was left undone, give it a mission to finish it.

Any agent's run, not only the one you are working on: if it failed, was lost, was
unprovisioned, or reported an error, read its transcript for the cause and give the Coder
Agent a mission to fix it: the repo, what failed, the evidence, and the run id. Then go back
to the agent you are working on.
```

Why it looks like this:

- **One agent at a time,** so every change has one cause and can be reverted alone.
- **"Done" is written down before the trial,** from real runs, so the comparison is not a
  feeling.
- **The last paragraph** makes the supervisor the place a quiet failure is noticed — a run
  that "succeeded" but reported an error used to go unseen.
