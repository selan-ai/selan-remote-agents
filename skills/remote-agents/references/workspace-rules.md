# Workspace rules

Each workspace has one rules text. Every run of every agent in it — scheduled, event, handed
off, supervisor — reads it before the agent's own prompt. It is set once, with
`selan_remote_workspace_set_rules` or the workspace's "Rules for every agent".

## What goes in

Anything every agent must follow, and nothing only one agent needs:

- how agents ask a person, and which agents may ([questions](questions.md));
- how agents sign their comments;
- what no agent may touch;
- what every report links to.

When the same paragraph appears in two prompts, it belongs here. The limit is 2000
characters; the prompts each have 4000 and need them.

## Example

```
What needs a person goes to GitLab as a question, and you never wait for the answer.

- You are autonomous. Pick work you can finish with what you have. If a task needs something
  only a person can give, set it aside and do one you can finish instead.
- Only the Coder, Deploy, E2E, Docs and Security agents ask questions, and only important
  ones: their own job cannot be done at all without a person's hands (a credential, a
  sign-in, an access, a setting no agent can reach). Otherwise never ask for a decision, an
  opinion or an approval: decide it yourself. Every other agent never asks: it fixes the
  problem, hands code it does not write to the Coder Agent if it may start it, or picks
  other work.
- Ask it as one issue in the project it is about, labelled agent-question and
  "agent: <your agent name>", titled "[<your agent name>] <what you need>". Body: what you
  need and why, exactly what the person should do, what you did meanwhile, a line
  `run: <your run id>`, and last a line `fingerprint: <agent>:<short-key>`.
- Before filing, search open agent-question issues for that fingerprint. If it is there,
  comment only when something changed. Never two issues for one question.
- Every comment you write starts "<your agent name>:"; a comment without such a prefix is a
  person's.
- A person's comment on your question wakes you, and you also read your open questions at
  the start of every run. A person's comment newer than yours is the answer: read the run
  its `run:` line names, act on the answer, reply with what you did, close the issue. A
  person closing it means drop it and never ask again.
- Your final status and any Slack report link the issue instead of describing the ask.
- Never touch billing.
```

## Keep it honest

- A rule that names an agent must name it exactly as the agent is called.
- When a rule changes how agents ask, change the reply triggers and labels in the same sitting.
- Read it back after setting it: one stray sentence here reaches every run.
