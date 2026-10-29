---
name: maintain-and-deploy
description: Keeps a repository small, current and moving, and ships its default branch to production when it is clean.
ask: Create agents that maintain and deploy this repo
agents: [dependency-updater, dead-code-cleaner, mr-watcher, sentry-fixer, prod-deployer]
---
The Dependency Updater and the Dead Code Cleaner keep the code current and small, a little at a
time. The MR Watcher keeps merge requests from waiting on nobody. The Sentry Fixer fixes the
errors that reach users. The Prod Deployer ships the default branch when the pipeline, the
merged changes and staging logs are clean, and rolls back a deploy that raises errors.
