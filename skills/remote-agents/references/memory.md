# Memory

Each agent has its own memory, kept between runs. A run that starts from nothing repeats the
last run's work; a run that starts from a diary reads it all and still repeats it. Write a
**ledger**: items with a state, added when work starts, dropped when it is done.

## What to keep

| Keep | Example line |
| --- | --- |
| Each handoff and its state | `acme-api 401 erases git credential — coder run 3f1c2a9e — MR !35 merged — deployed 08:48 — verify next run` |
| Noise ruled out, with the reason | `"lease lost" warnings in worker — expected on scale-down — ignore` |
| What "done" means for a recurring job | `deploy: prod at main HEAD for every project with a deploy-prod job` |
| What it removed or closed, to undo | `closed acme-web !37 — branch feat/x @ 2386c1ec — 2026-10-06` |
| Repo facts the repo's own docs do not say | `acme-web: e2e job times out under load — retry once` |
| Where a rotation stands | `security: acme-api reviewed 2026-10-06 (auth, webhooks); next: acme-proxy` |

## What not to keep

- The task itself. Tomorrow's input says what to do.
- Anything a repository's CLAUDE.md already says.
- Secrets, tokens, or values from a connector.
- "Blocked" notes with no way out. They outlive the block: an agent once reported the same
  "a person must enable billing" every run for days after the answer was no.

## Rules for the prompt

- Say what to keep, and when to drop it: "Drop an issue once verified."
- Say to read memory first: "Follow up first" depends on it.
- Re-check any note that says something is blocked against the open questions. If no
  question backs it, the note is stale: drop it.
- When a note and the live state disagree, the live state wins, and the note is fixed.
