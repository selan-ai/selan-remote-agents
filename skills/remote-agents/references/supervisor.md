# The supervisor

A workspace can have one supervisor: an agent whose only work is the other agents. It reads
every agent, run and transcript in its workspace, and can change an agent's prompt, model,
effort or schedule, start a run with a mission, and revert a change. It cannot reach any
connector: no repositories, no logs, no Slack. When something needs doing on the product, it
gives a worker a mission.

## The mission is the runbook

The supervisor's prompt is its mission, and it is also the condition for waking it. Every
time a run in the workspace ends, a judge reads that ending against the mission; when the
mission says to act on endings like that one, the supervisor wakes at once. There is no
separate "wake when" setting.

```
Any agent's run: if it failed, was lost, was unprovisioned, or reported an error, read its
transcript for the cause and give the coder a mission to fix it: the repo, what failed, the
evidence, and the run id.
```

That one paragraph is both "wake me when a run ends badly" and "then do this".

## Waking

- **An ending matches the mission** — at once.
- **The time or the runs it asked for** — its last run says when to wake next: a time, runs
  to wait for, or both.
- **Its clock** — 24 hours after its last run, when it said nothing.
- **A person presses Run.**

Each wake's input lists the runs that ended since it last woke, so nothing is missed between
wakes. It never waits inside a run: it starts what it needs, says when to wake, and ends.

## Good missions

**Cheaper without doing less**, one agent at a time:

```
Pick the agent on the strongest model that costs the most and is not yet settled.
1. Write in memory what "done" means for it, from its last runs.
2. Start one run of it on the cheaper model, and wake after that run. Compare.
3. Switch it only if the work was still done for less. Wake after its next 5 runs, and
   revert if any did less.
4. Record the result and pick the next agent.
```

**Finish what was left undone:**

```
After each run: if it timed out or its report says work was left undone, give that agent
a mission to finish it.
```

## What it should not do

- Fix product code itself. It has no repository access by design.
- Rewrite several agents in one wake. One agent per wake keeps every change attributable.
- Change itself. Its own mission is a person's to change.
