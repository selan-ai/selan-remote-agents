---
name: grow-test-coverage
description: Raises a repository's test coverage toward 100% and keeps its tests trusted.
ask: Create agents that grow my test coverage
agents: [e2e-test-writer, unit-test-writer, flake-hunter]
---
The E2E Test Writer works through everything a client can reach, a few endpoints or flows a
run, against the real service and database. The Unit Test Writer gives what was merged since
its last run tests that fail if the change is reverted. The Flake Hunter fixes the tests that
fail and pass on the same code, so the new ones stay worth trusting.
