---
name: keep-app-secure
description: Finds and fixes real security holes in a repository, and keeps its dependencies free of known vulnerabilities.
ask: Create agents that keep my app secure
agents: [security-reviewer, dependency-updater]
---
The Security Reviewer goes through the repository one area at a time and fixes each real hole
with a test that proves it. The Dependency Updater takes the fixed version of any package with
a known advisory first, and only a release old enough to trust.
