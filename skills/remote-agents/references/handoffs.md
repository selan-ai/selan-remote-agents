# Handoffs: workers hand work on

Two kinds of agent, and they never swap roles.

| | Workers | The supervisor |
| --- | --- | --- |
| Do | One job on the product: deploy, watch logs, fix code | Manage the other agents |
| Touch | Repositories, logs, issues, Slack | Agents' prompts, models, schedules |
| Hand to | Another worker, with a message | A worker, with a mission |
| Never | Edit another agent | Reach a repository or a connector |

![A fleet of agents and its handoffs](../../../assets/fleet.svg)

## The coder is the end of the line

Agents that find problems — a log watcher, an error tracker, a deploy agent that sees a red
main — do not fix code themselves. They hand it to one agent that writes code well, and they
verify the fix after it ships. That keeps the finding agents cheap and the code changes in
one style.

```
log watcher ──┐
error tracker ┼──> coder ──> MR ──> pipeline + review ──> merge ──> deploy
deploy agent ─┘                                                      │
     ▲                                                               │
     └────────────── verifies on its next run ───────────────────────┘
```

Agents that own a code area — dependencies, dead code, components — change it themselves.

## Writing the handoff

The message is a report the receiver can check, not an order. Start with where to work:

```
Repo: acme-api (start from origin/main). Fix: one 401 from the git proxy erases the run's
only git credential for the rest of the run.

Evidence: run 3f1c2a9e (2026-10-06 07:23 UTC) cloned, got one 401 mid-fetch, and every
later git command failed with "could not read Password". The credential helper ran `erase`
on the 401 and emptied the file.

Wanted: a helper that answers `get` from the file and ignores `store` and `erase`. A test
against a real git server: after one 401, the next command still authenticates.

After the prod deploy, confirm with: the same clone in a fresh run.
```

| Include | Why |
| --- | --- |
| Repo and starting point | The receiver starts in an empty machine |
| What fails, with counts and the time window | So it can tell a fix from a coincidence |
| The filter or command that finds it | So it can confirm the fix worked |
| The root cause with file:line, if known | So it does not rediscover it |
| The fix you suggest, and a test that fails without it | The receiver may choose another |
| How to confirm after deploy | The finder verifies on its next run |

## Not twice

- **Before handing off, check it is not already handed off** — your memory first, then the
  receiver's runs if you can read them.
- **Keep every handoff in memory** with the receiver's run id and its state; follow up on the
  next run; drop it once verified.
- **A fix that failed twice goes no further by itself:** record it, report it, stop handing
  it off. That is a decision for the supervisor or a person, not a third attempt.

## Callers

The receiving agent's callers list must name the sender. Keep the graph small: three or four
senders into the coder, one sender into the log watcher (the deploy agent, after each
deploy). See [triggers](triggers.md).
