# The agent's prompt

The system prompt says who the agent is and how it works. What to do *this time* is the run's
input — the schedule's prompt, the event sentence, or the message another agent sent. Keep
them apart: a schedule prompt of one line ("Check logs") and a prompt that knows how.

![The shape of an agent prompt](../../../assets/agent-anatomy.svg)

## The shape

Every prompt that works unattended has the same seven parts, in this order.

| Part | Says | Example |
| --- | --- | --- |
| **Role** | The one job, and what it never does | "You deploy to prod. Nothing else: no fixes, no retries, no log reading." |
| **Schedule** | When it runs, matching its trigger | "Every hour from 08:00 to 18:00 (cron `0 8-18 * * *`)." |
| **Memory** | What to keep across runs, and when to drop it | "Keep each issue you handed off and its state; drop it once verified." |
| **Tools** | Which connectors, for what, and what never to print | "GitLab MCP only, plus Slack for the report. Never print credentials." |
| **Steps** | Numbered: follow up, look, choose, act, verify | see below |
| **Report** | Where, how long, and only when something happened | "One Slack message, at most 4 sentences, only when you deployed." |
| **Final answer** | What the run's last message must contain | "One line per project: current, deployed, or skipped with reason." |

## The steps

The order is the same in almost every agent:

1. **Follow up first.** Finish what an earlier run started (an open MR, a handoff to verify)
   before starting anything new. Without this, every run opens a new MR beside the last one.
2. **Look.** Exactly where, over which window, with which filter.
3. **Choose.** The bar something must clear to be acted on, and how many per run ("at most
   two handoffs per run"). Say that finding nothing is a good outcome — otherwise the agent
   invents work.
4. **Act.** The smallest change, in the repository's own style, with a test that fails
   without it.
5. **Verify.** Pipeline, review, merge, deploy, then health and ten minutes of error logs.
   Revert if they rise.

## Rules that save runs

- **One job.** "Deploy and fix what is red" is two agents: one deploys, the red goes to the
  agent that writes code. An agent with two jobs does the easier one.
- **Say what it never does.** "Nothing else" is the line that stops a deploy agent from
  editing code at 3 a.m.
- **Cap every run.** At most N MRs, N handoffs, N closed issues. The rest waits for the next
  run, which is never more than a day away.
- **Name the exact place.** Project, channel, log filter, label. "Report to Slack" without a
  channel is a guess every run.
- **Write handoffs as reports the receiver can check,** not orders: evidence, counts, the
  filter that finds it, the suggested fix, the test, and how to confirm it worked.
- **Keep it under 3500 characters.** The limit is 4000; the last 500 are what you need the
  day something goes wrong. When a prompt grows, move what every agent shares into the
  [workspace rules](workspace-rules.md).
- **Prefer the repository's own rules.** "Read the repository's CLAUDE.md after cloning; it
  overrides this prompt." The agent starts in an empty machine and loads nothing by itself.

## Model and effort

Start every agent on the strongest model. Once it has a record of what "done" looks like,
try a cheaper model on one run and compare ([supervisor](supervisor.md) can do this for you).
Mechanical agents — deploy, cleanup, refreshing a price list — usually hold up on a smaller
model; agents that judge code or security usually do not.
