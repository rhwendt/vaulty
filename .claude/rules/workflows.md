---
description: Standard development and bug fix workflows for subagent collaboration
---

# Development Workflows

These workflows describe ideal delegation patterns. Subagents coordinate with each other automatically based on their descriptions.

## Complete Development Workflow

For new features or significant changes:

1. **Project Manager** - Creates/updates tasks
2. **Architect** - Designs high-level architecture (if major feature)
3. **Software Designer** - Designs code structure and patterns
4. **Developer** - Implements code
5. **Tester** - Writes and runs tests
6. **Auditor** - Reviews for quality and security
7. **Git Helper** - Commits and pushes
8. **Documenter** - Updates documentation
9. **Deployment** - Deploys to environments
10. **Project Manager** - Marks task complete

## Quick Bug Fix Workflow

For bug fixes and hotfixes:

1. **Debugger** - Investigates root cause
2. **Developer** - Implements fix
3. **Tester** - Adds regression test
4. **Auditor** - Reviews fix
5. **Git Helper** - Commits fix
6. **Deployment** - Deploys hotfix (if needed)

## Automatic Delegation

Claude automatically delegates to subagents based on your request:

| When You Say... | Subagent Delegated |
|-----------------|-------------------|
| "commit", "push", "PR" | git-helper |
| "implement", "write code", "fix bug" | developer |
| "test", "run tests", "coverage" | tester |
| "review", "audit", "security check" | auditor |
| "document", "README", "API docs" | documenter |
| "create project", "track task" | project-manager |
| "design architecture", "ADR" | architect |
| "deploy", "release", "CI/CD" | deployer |
| "debug", "error", "not working" | debugger |
| "design pattern", "refactor" | software-designer |
| "design a new project", "plan project" | project-designer |
