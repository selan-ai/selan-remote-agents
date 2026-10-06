# Reporting

A run ends twice: once as a **state** Selan records (succeeded, failed, timed out, lost,
unprovisioned, cancelled), and once as the agent's own **report**: a level and one line.
The state says whether the machine finished; the report says whether the work did.

A run can be `succeeded` with an `error` report: the agent ran to the end and could not
clone the repository. Watch the report, not the state.

## The levels

| Level | Means | Example line |
| --- | --- | --- |
| `ok` | It did what it was asked; finding nothing counts | `Checked 09:00–11:00 logs: only known noise, no handoff.` |
| `warn` | Part of the work is not done, or a person should look | `Closed 2 stale MRs; no branches deleted (no delete tool): acme/ops#2` |
| `error` | It failed at what it was asked | `Clone failed (401, empty git credential); nothing done.` |

The line is under 160 characters, says the outcome first, and links the issue or MR instead
of describing it.

## Slack

- **Only when something happened**: a deploy, a handoff, a verified fix, a merged MR, a
  revert, a question filed. A quiet run is silent.
- **One message per run,** at most four to six lines, one channel per kind of work.
- **Link, do not describe.** The MR, the issue, the run.

## The final answer

The run's last message is what a person reads when they open the run, and what the
supervisor reads against its mission. Ask for it explicitly in the prompt:

```
Final answer: what you followed up and its state, what you looked at, what you chose and
why, the run ids you started, and what needs a person.
```

A final answer that only says "Done." makes the run impossible to audit.

## Unverified is a word

When something could not be checked — a deploy's logs closed to the agent, a fix that has
not met real traffic yet — the report says "unverified", not "done". The next run, or the
agent that handed it off, verifies.
