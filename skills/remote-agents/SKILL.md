---
name: remote-agents
description: Use when designing, writing, reviewing or auditing Selan remote agents (Reactor) — an agent's prompt, its schedule or event trigger, handoffs between agents, workspace rules, the supervisor's mission, questions agents ask people, memory, tags or reports — or when agents fail quietly, ask too much, or cost more than they deliver.
allowed-tools: mcp__selan__selan_remote_workspaces_list mcp__selan__selan_remote_agents_overview mcp__selan__selan_remote_agents_canvas mcp__selan__selan_remote_agent_detail mcp__selan__selan_remote_agent_triggers_list mcp__selan__selan_remote_agent_connectors_list mcp__selan__selan_remote_agent_runs_list mcp__selan__selan_remote_agent_run_get Read Grep Glob
---

# Selan remote agents

A remote agent is a Claude Code session Selan starts on a machine nobody owns, wakes on a
trigger, and shuts down when it reports. Many of them, each with one job, can keep a product
healthy with nobody watching — if each one is built to finish its work alone.

Two modes. Ask which one if the request does not say.

- **Design**: a new agent, or a change to one. Ends in a full definition the user approves.
- **Audit**: the agents a workspace already has, read through Selan MCP and checked against
  the practices below. Ends in a ranked list of fixes. Changes nothing.

## The practices

Each line is a rule; the reference says why and shows it done.

1. **One job per agent, written as a prompt with a fixed shape.** Role, schedule, memory,
   tools, numbered steps, report, final answer. [agent-prompt](references/agent-prompt.md)
2. **Something wakes every agent.** A schedule, an event sentence, or another agent.
   An agent nothing wakes never runs. [triggers](references/triggers.md)
3. **Agents are autonomous.** They pick work they can finish and set the rest aside. Only a
   few may ask a person, only for hands (a credential, a sign-in, an access), labelled for
   the agent so the reply wakes it. [questions](references/questions.md)
4. **Workers hand work on; one supervisor manages agents.** A worker never edits another
   agent. [handoffs](references/handoffs.md), [supervisor](references/supervisor.md)
5. **What every agent must follow is said once,** in the workspace rules, not in fourteen
   prompts. [workspace-rules](references/workspace-rules.md)
6. **Memory is a ledger,** not a diary: what was handed off and its state, what was ruled
   out and why. [memory](references/memory.md)
7. **Every run ends in one honest line** at ok, warn or error, and speaks in Slack only when
   something happened. [reporting](references/reporting.md)
8. **Track what tells you the fleet works:** report level over run state, cost per agent,
   handoff chains. Tags and labels make it findable. [tracking](references/tracking.md)
9. **Least privilege per agent,** one sign-in per connector it needs, nothing else.
   [connectors](references/connectors.md)
10. **Merge when green, revert on errors, cap each run, never attack anything live.**
    [safety](references/safety.md)

## Design

1. Name the one job in a sentence. Two jobs are two agents.
2. Pick what wakes it ([triggers](references/triggers.md)) and what it hands to whom.
3. Start from the closest [example](examples/): deploy, log watcher, coder, cleanup,
   security. Write the prompt in the shape of [agent-prompt](references/agent-prompt.md),
   under 4000 characters.
4. List its connectors, and whether it may ask a person (most may not).
5. Show the whole definition — name, model, effort, prompt, trigger, connectors, callers,
   tags — and ask before creating anything.

## Audit

Read, in this order: `selan_remote_workspaces_list` (rules), `selan_remote_agents_overview`,
`selan_remote_agent_detail` for each agent, `selan_remote_agent_triggers_list`,
`selan_remote_agents_canvas` (last runs, expired sign-ins, spend), and
`selan_remote_agent_runs_list` with `selan_remote_agent_run_get` for the last runs that
ended badly. Then check:

| What you see | What it breaks | Reference |
| --- | --- | --- |
| An agent with no trigger and no caller | It never runs | [triggers](references/triggers.md) |
| A trigger aimed at an agent that no longer exists | Noise, and a fire that fails | [triggers](references/triggers.md) |
| A run `succeeded` whose report is `error` | A failure nobody acts on | [reporting](references/reporting.md) |
| The same report line run after run | A blocker it should set aside or hand on | [questions](references/questions.md) |
| Open questions nobody answered, or many askers | People drowning in asks | [questions](references/questions.md) |
| A prompt over 3500 characters, or two jobs in one | No room to fix it; work done half | [agent-prompt](references/agent-prompt.md) |
| The same paragraph in several prompts | Drift between copies | [workspace-rules](references/workspace-rules.md) |
| A worker that updates other agents | Two managers | [supervisor](references/supervisor.md) |
| Several agents merging and deploying the same repo | Two pipelines deploying at once | [safety](references/safety.md) |
| An expired sign-in | Every run of that agent fails | [connectors](references/connectors.md) |
| One agent costing several times its peers | Spend without output | [tracking](references/tracking.md) |

Report per finding: the agent, what you saw (quote it), the fix in one sentence, and the
reference. Most severe first, at most ten. Then one question: which to apply.

## Hard limits

| Limit | Value |
| --- | --- |
| Agent prompt | 4000 characters |
| Workspace rules | 2000 characters |
| Event trigger sentence, schedule prompt | 4000 characters |
| Tags per agent | 8 |
| Run length | the workspace's run limit; credentials last as long as the run |
