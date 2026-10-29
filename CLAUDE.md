# selan-remote-agents

A Claude Code plugin. What it teaches is in `README.md`; this file is what the files will not
tell you.

## Commands

```sh
claude plugin validate .claude-plugin/plugin.json            # manifests and the SKILL.md
claude -p "/selan-remote-agents:remote-agents audit" --plugin-dir . --output-format json
```

## Rules

- **Public repository.** No agent id, run id, company id, Slack channel id, email, service
  account or project name from a real workspace. Examples use `acme`, `#deploys`, made-up uuids.
- **`SKILL.md` stays short:** the practices as one line each, the two modes, and links.
  Everything that explains goes in `references/`, one topic per file.
- **A practice is written from a run that went wrong,** not from taste. Each reference says the
  failure the rule prevents.
- **Diagrams are hand-written SVG** with a white background, so they read in light and dark
  themes. Check one by rendering it in a browser at its own size.
- **`agents/` is for installing into Reactor, never loaded by Claude Code.** `plugin.json`'s
  `"agents": []` replaces the default scan; without it every file there becomes a subagent of
  everyone who installs the plugin, picked by its description, with its Setup still unfilled.
- **An agent file names no company.** What differs between companies goes in the prompt's
  `## Setup` section as `- Label: what it is`; the Repository line says "on GitHub or GitLab",
  and `mcpServers` lists only connectors every install needs. A prompt stays under 3500
  characters with each Setup line filled.
- **Limits quoted here must match Selan's:** prompt 4000 characters, workspace rules 2000,
  tags 8. Change them together with the product.

## Releasing

Bump `version` in both `.claude-plugin/plugin.json` and `marketplace.json`, then
`gh release create vX.Y.Z`.
