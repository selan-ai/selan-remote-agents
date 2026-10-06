# Tracking: tags, labels and what to watch

## Tags

Tags find agents on the canvas. They change nothing a run does. At most eight per agent,
lower case.

| Tag | Use |
| --- | --- |
| `autonomous` | Runs on its own schedule with no person in the loop |
| an area: `ux`, `security`, `deploy` | Groups the agents that share a board or a channel |
| a stage: `trial` | An agent being tried before it gets a schedule |

Tag by what you filter on. A tag nobody filters by is noise.

## Labels on your tracker

| Label | Means |
| --- | --- |
| `agent-question` | An agent needs a person's hands. One column on the board. |
| `agent: <name>` | Which agent asked; the reply wakes that agent. Only for agents that may ask. |
| A flow's columns, e.g. `ux-investigate`, `ux-approval`, `ux-approved` | A proposal moving through people and agents; moving a card is the instruction |
| `keep` | Cleanup agents leave this MR alone |

Create them as group labels, so every project shares them and the board sees all of them.

## What to watch

| Signal | Where | Healthy |
| --- | --- | --- |
| Report level per run | The canvas, the runs list | Mostly `ok`; every `error` has a handoff or a question |
| The same report line repeating | Last runs of an agent | Never more than two in a row |
| Cost per agent per week | The canvas's spend | Stable; a spike has a reason |
| Cost per useful outcome | Spend ÷ MRs merged or issues fixed | Falling as agents settle |
| Handoff chains | `X Agent (run …) started you` in a run's input | Each ends in a merged fix or a recorded stop |
| Open questions | The `agent-question` column | A handful at most |
| Expired sign-ins | The canvas, per agent | Zero |
| Timeouts | Runs `timed_out` | Rare; each one gets a finishing mission |

## Ids everywhere

Put the run id in everything an agent leaves behind — the question's `run:` line, the MR
description, the handoff message. Every artefact then leads back to the transcript that
made it.
