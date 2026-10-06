# Questions: when an agent needs a person

Agents are autonomous. An agent that asks a person every time something is hard is a ticket
generator, and the person stops reading. The rule: **pick work you can finish; ask only for
hands.**

![The question loop](../../../assets/question-loop.svg)

## Who may ask

Most agents never ask. When they hit something they cannot do, they:

1. fix it themselves, if it is in their job;
2. hand it to the agent that writes code, if it is code ([handoffs](handoffs.md));
3. set it aside and do other work.

A few agents may ask, because their job ends where only a person can act. Name them in the
[workspace rules](workspace-rules.md):

| Agent | Why it may ask |
| --- | --- |
| Coder | It is the end of the line for every fix; a fix that needs a grant, a CI variable or a secret has nowhere else to go |
| Deploy | A deploy blocked by a permission or setting needs a person, and no agent can grant one |
| End-to-end tests | Test accounts and their secrets expire, and only a person can renew them |
| Docs | Some facts live only in a person's head |
| Security | A fix that would break the system, or a design it cannot understand from the code |

## What is a question

| Is a question | Is not a question |
| --- | --- |
| A credential, token or secret only a person can create | Which of two fixes to choose: decide |
| A sign-in that expired | Whether a change is wanted: propose it as an MR |
| An access or grant no agent holds | An approval: merge when green, revert on errors |
| A setting no agent can reach | Something another agent can do: hand it on |

## How it is asked

One issue, in the project it concerns, so it shows on the team's board:

```
Title   [Deploy Agent] Grant deploy access to acme-api's prod project
Labels  agent-question, agent: Deploy Agent

Body
deploy-prod for acme-api fails with "permission denied on run.services.update"
since 2026-10-05; the deploy service account lost roles/run.developer.

What a person should do: grant roles/run.developer on acme-prod to
deploy@acme-ci.iam.gserviceaccount.com.

Meanwhile: acme-api stays on the last good revision; every other project deploys.

run: 3f1c2a9e-7b4d-4e8a-9c21-5d6f0a1b2c3d

fingerprint: Deploy Agent:acme-api-run-developer
```

- **`agent: <name>` label.** It routes the reply to the agent that asked. One label per
  agent that may ask; nothing else gets one.
- **`run:` line.** The run that asked. When the reply wakes the agent, it reads that run
  (`selan_remote_agent_run_get`, `selan_remote_agent_run_transcript`) and knows where it left
  off. A run can read its own agent's runs.
- **`fingerprint:` line.** Search open questions for it before filing. One question, one
  issue, ever.
- **What it did meanwhile.** The person learns nothing is stuck.

## Comments

Agents often post as the same account a person uses, so the text says who wrote it:

```
Deploy Agent: still failing on 2026-10-06 03:00, same error. Nothing else changed.
```

**Every agent comment starts with its name and a colon. A comment without one is a person's.**
The reply trigger relies on it, and so does the agent reading the thread.

The agent's last comment, after acting on the answer:

```
Deploy Agent: deployed acme-api a1b2c3d -> e4f5a6b after the grant; health 200. Closing.
```

## The reply trigger

Each agent that may ask has one event trigger:

```
When a person comments on a GitLab issue in any acme project labelled agent-question
and "agent: Deploy Agent" (a comment that does not start with an agent's name and a colon),
read the run its `run:` line names, then act on the answer.
```

It also reads its open questions at the start of every scheduled run, in case a reply
arrived while comments were held ([triggers](triggers.md)).

## Closing

- The agent closes the issue after acting on the answer, saying what it did.
- A person closing it means: drop it, never ask again.
- An answer that needs code the asking agent does not write goes to the coder, and the
  issue's label moves to `agent: Coder Agent` so the next reply wakes the right agent.

## The board

One column on the team's board, filtered by `agent-question`, is every open ask. If it has
more than a handful of cards, too many agents are asking, or asking for decisions.
