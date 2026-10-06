# Triggers: what wakes an agent

An agent with nothing to wake it never runs. There are three ways in, and a person's Run
button.

| Trigger | Wakes the agent when | Its input is |
| --- | --- | --- |
| **Schedule** | A cron time comes | The schedule's prompt |
| **Event** | An event the workspace webhook receives matches a sentence | The sentence, then what happened |
| **Agent** | An agent on its callers list starts it | That agent's message |

## Schedules

```
cron      23 4 * * *
timezone  Europe/Vilnius
prompt    Close every MR nobody touched for 7 days, delete unused branches, and report.
```

- **Keep the prompt to one line.** The how belongs in the agent's prompt; the schedule says
  what this run is for.
- **Avoid `:00` and `:30`.** Every schedule in the world fires then. Pick `:23`, `:47`.
- **Write the schedule into the agent's prompt too** ("Schedule: daily at 04:23"), so the
  agent knows how long until its next run and what the next run will verify.
- **Match frequency to how fast the thing changes.** Logs every two hours, dependencies
  nightly, performance trials three times a week. Every run costs, even the empty ones.
- **Spread heavy agents across the night** so they do not deploy over each other.

## Event triggers

An event trigger is a sentence. A small judge model reads every event the workspace's
webhook receives — a GitLab issue moved, a comment, a JSON your own service posted — and
fires the agent when the event is what the sentence describes.

```
When a GitLab issue in any acme project is moved to ux-approved,
merge and deploy that issue's proposal.
```

- **One sentence is both the condition and the instruction.** The judge reads only the
  condition; the agent receives the whole sentence.
- **Say "moved to X"** for a board column. A column is a label, and the judge is told which
  labels this event added.
- **Route by label, not by title.** "labelled agent-question and `agent: Deploy Agent`" is
  exact; a title is free text.
- **Exclude what agents write.** Agents often comment as the same account a person uses. Make
  every agent comment start with its name and a colon, and say in the sentence: "a comment
  that does not start with an agent's name and a colon".
- **A comment is held while any run an event started is still going.** That stops two
  agents answering each other forever; it also means a reply posted then is not delivered.
  Agents should also read their questions at the start of each scheduled run.

### Examples

```
When a GitLab issue in any acme project is moved to ux-investigate,
investigate it and propose a fix.

When a person comments on a GitLab issue in any acme project labelled agent-question
and "agent: Coder Agent" (a comment that does not start with an agent's name and a colon),
read the run its `run:` line names, then act on the answer.
```

## Agent triggers (callers)

Every agent has a list of other agents that may start it with a message of their own. That
list is the handoff graph: who may give whom work. See [handoffs](handoffs.md).

- Add a caller only for a real handoff: "the log watcher hands fixes to the coder".
- An agent may start only agents in its own workspace.
- The started run is told which agent started it and that the message came from an agent.

## Housekeeping

- When an agent is deleted, check no trigger still points at it.
- Pause a trigger rather than deleting it when you may want it back: a deleted trigger loses
  its fire history.
- After a run fails because of a trigger's wording, fix the wording, not the agent.
