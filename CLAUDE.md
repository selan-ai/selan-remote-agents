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
- **Limits quoted here must match Selan's:** prompt 4000 characters, workspace rules 2000,
  tags 8. Change them together with the product.

## Releasing

Bump `version` in both `.claude-plugin/plugin.json` and `marketplace.json`, then
`gh release create vX.Y.Z`.
