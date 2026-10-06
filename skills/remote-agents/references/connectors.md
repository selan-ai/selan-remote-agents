# Connectors

Each agent has its own sign-in to each connector it uses, and nothing else. An agent that
watches logs does not need Slack write access to every channel; an agent that deploys does
not need the logs.

## Kinds of sign-in

| Kind | Example | Set up by |
| --- | --- | --- |
| **OAuth** | GitLab, Slack, Sentry | A person opens the agent's sign-in link and approves |
| **Service account** | Google Cloud Logging, Cloud Run, BigQuery | Selan makes the agent its own account; a person grants it per project |
| **Itself** | Selan, web search | Nothing: the run reaches it as itself |
| **Database** | Postgres | A connection added on the agent's Connectors page |

## Rules

- **Only what the job needs.** List the connectors in the prompt's Tools line, with what each
  one is for: "Logging MCP on dev and prod, only to check a deploy."
- **Read-only where you can.** A log watcher gets the viewer role, not admin.
- **Grant per project.** A Google Cloud connector reaches only the projects granted to it.
- **Never billing.** No agent gets billing access, and no prompt relies on a metric that
  needs it.
- **Never print a credential.** Every prompt says so; a run's transcript is read by people
  and by other runs.

## When a sign-in expires

Every run of that agent fails the same way until a person signs it in again. The canvas
shows expired sign-ins per agent. An agent that may ask files one question with the sign-in
link's place; the others report it at `error` and the supervisor or a person picks it up.

## What a run can read about itself

A run can read its own agent's earlier runs and transcripts through Selan MCP, and the runs
it started. It cannot read another agent's: that transcript holds what the other agent
fetched through its own sign-ins.
