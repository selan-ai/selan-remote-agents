# Examples

Complete agents, each with its settings and its whole prompt. They are adapted from a fleet
that runs a real product; names, channels and projects are placeholders (`acme`, `#deploys`).

| Example | Shows |
| --- | --- |
| [deploy-agent](deploy-agent.md) | A mechanical agent on a cheaper model; two handoffs; "nothing else" |
| [log-watcher](log-watcher.md) | Follow up first, a bar to act on, caps per run, verifying after deploy |
| [coder-agent](coder-agent.md) | The end of the line for fixes; no schedule; may ask |
| [cleanup-agent](cleanup-agent.md) | Destructive work with a way back for everything |
| [security-agent](security-agent.md) | A rotation through repos, a findings ledger, never attacking live |
| [supervisor-mission](supervisor-mission.md) | A mission that is also its own wake condition |

The workspace rules they all share are in [workspace-rules](../references/workspace-rules.md).
