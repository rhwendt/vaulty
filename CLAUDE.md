# Vaulty: Context Memory System for Claude

A context memory system for maintaining consistent behavior and best practices across tasks and projects.

## Quick Start

1. **Check [[config]]** - Your personal preferences (ALWAYS CHECK FIRST!)
2. **Browse `.claude/rules/`** - Best practices auto-loaded into every session
3. **Use specialized agents** - Domain experts in `.claude/agents/`

## How It Works

This system provides:
- **Rules** (`.claude/rules/`) - Best practices and guidelines (auto-loaded)
- **Agents** (`.claude/agents/`) - Specialized personas for specific tasks
- **Projects** (`projects/`) - Structured project management
- **Config** (`config.md`) - Your personal settings and preferences

## 🔧 User Configuration (CHECK FIRST!)

> [!CRITICAL]
> **ALWAYS read [[config]] BEFORE any operation!**
>
> The user's `config.md` file contains their personal preferences and settings. Every agent and rule references this config. You MUST check it to:
> - Use user's preferred tools, frameworks, and languages
> - Follow user's code style and formatting preferences
> - Deploy to user's environments and infrastructure
> - Work in user's local directory structure
> - Match user's team's workflow and standards
>
> **Config Location**: `config.md` (if it doesn't exist yet, ask user to create it from `config.template.md`)

## Core Rules (Auto-loaded)

All files in `.claude/rules/` are automatically loaded. Key rules include:

- **git-workflow.md** - Git operations, commits, branching, PRs
- **testing-qa.md** - Testing strategies and QA processes
- **code-review.md** - Code review guidelines and standards
- **architecture-design.md** - Architecture patterns and design principles
- **deployment.md** - Deployment procedures and best practices
- **documentation.md** - Documentation standards
- **project-management.md** - Task tracking and project organization
- **communication.md** - Communication standards and templates

## Specialized Subagents (Automatic Delegation)

Claude Code automatically delegates to specialized subagents based on your requests. All subagents are defined in `.claude/agents/` with YAML frontmatter.

### Development Workflow (Opus Model)
- **Developer** (`developer`) - Writing code, implementing features, fixing bugs
- **Tester** (`tester`) - Writing tests, running test suites, coverage
- **Auditor** (`auditor`) - Code review, security audits, quality checks
- **Software Designer** (`software-designer`) - Design patterns, code structure, refactoring

### Project & Architecture (Opus Model)
- **Project Designer** (`project-designer`) - New project planning from requirements
- **Architect** (`architect`) - High-level design, architectural decisions, ADRs

### Operations (Sonnet Model)
- **Git Helper** (`git-helper`) - Version control operations
- **Deployment** (`deployer`) - Deployments, CI/CD, releases
- **Debugger** (`debugger`) - Investigating bugs, troubleshooting
- **Project Manager** (`project-manager`) - Managing projects, tracking tasks
- **Documenter** (`documenter`) - Creating/updating docs, READMEs, API docs

**How It Works**: Subagents are automatically invoked based on their `description` field in the YAML frontmatter. You don't need to manually invoke them - Claude will delegate to the appropriate subagent based on your request.

## Subagent Collaboration Workflows

These workflows describe ideal delegation patterns. Subagents may coordinate with each other automatically.

### Complete Development Workflow
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

### Quick Bug Fix Workflow
1. **Debugger** - Investigates root cause
2. **Developer** - Implements fix
3. **Tester** - Adds regression test
4. **Auditor** - Reviews fix
5. **Git Helper** - Commits fix
6. **Deployment** - Deploys hotfix (if needed)

## Automatic Delegation Patterns

Claude automatically delegates to subagents based on your request:

| When You Say... | Subagent Delegated | Rules Referenced |
|-----------------|-------------------|------------------|
| "commit", "push", "PR" | git-helper | git-workflow |
| "implement", "write code", "fix bug" | developer | architecture-design, code-review |
| "test", "run tests", "coverage" | tester | testing-qa |
| "review", "audit", "security check" | auditor | code-review, testing-qa |
| "document", "README", "API docs" | documenter | documentation |
| "create project", "track task" | project-manager | project-management |
| "design architecture", "ADR" | architect | architecture-design |
| "deploy", "release", "CI/CD" | deployer | deployment |
| "debug", "error", "not working" | debugger | (multiple) |
| "design pattern", "refactor" | software-designer | architecture-design |
| "design a new project", "plan project" | project-designer | architecture-design, project-management |

## Best Practices

### Always ✅
- Check `[[config]]` first for user preferences
- Reference relevant `.claude/rules/` files
- Delegate to appropriate subagents automatically
- Update project/task status as work progresses
- Document decisions and rationale
- Test code before committing
- Review code before merging

### Never ❌
- Skip checking user config
- Skip referencing rules for standard operations
- Commit code without testing and review
- Make architectural decisions without consulting `.claude/rules/architecture-design.md`
- Deploy without consulting `.claude/rules/deployment.md`
- Commit secrets or credentials

## Project Structure

### Creating New Projects
1. Use Project Manager Agent
2. Copy from `projects/_templates/project-overview-template.md`
3. Create structure in `projects/project-name/`

### Creating New Tasks
1. Use Project Manager Agent
2. Copy from `projects/_templates/task-template.md`
3. Use tags: `#task #status/pending #priority/medium`

## Customization

This system is a template. You can:
- Add new rules in `.claude/rules/` (auto-loaded)
- Create custom subagents in `.claude/agents/` (YAML frontmatter format)
- Modify existing subagents and rules
- Add project-specific documentation
- Update `config.md` with your preferences

## Notes

- This is an Obsidian vault - use `[[wiki-links]]` and tags
- **Rules** in `.claude/rules/` are auto-loaded into every session
- **Subagents** in `.claude/agents/` use official Claude Code format with YAML frontmatter
- **Automatic delegation**: Subagents are invoked based on their description field
- **Projects** directory contains work-in-progress and completed projects
- Always check `[[config]]` for personal settings first
