---
name: publish-open-source
description: Keeps an open-source package tested, current and reviewed, and publishes a new version when users would notice.
ask: Create agents that publish my open-source package
agents: [package-publisher, unit-test-writer, dependency-updater, mr-watcher]
---
The Package Publisher releases a new version to its registry when the default branch has changes
users would notice, and writes the release. The Unit Test Writer covers each merge first, the
Dependency Updater keeps what users install safe, and the MR Watcher keeps contributors' merge
requests from going unanswered.
