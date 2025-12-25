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

## Specialized Agents

Invoke these agents based on the task (see `.claude/agents/` for details):

### Development Workflow
- **Developer Agent** - Writing code, implementing features, fixing bugs
- **Tester Agent** - Writing tests, running test suites, coverage
- **Auditor Agent** - Code review, security audits, quality checks
- **Software Design Agent** - Design patterns, code structure, refactoring

### Project & Architecture
- **Project Designer Agent** - New project planning from requirements
- **Architect Agent** - High-level design, architectural decisions, ADRs
- **Project Manager Agent** - Managing projects, tracking tasks

### Operations
- **Git Agent** - Version control operations
- **Deployment Agent** - Deployments, CI/CD, releases
- **Debugger Agent** - Investigating bugs, troubleshooting

### Documentation
- **Documentation Agent** - Creating/updating docs, READMEs, API docs

## Agent Collaboration Workflow

### Complete Development Workflow
1. Project Manager → Creates/updates tasks
2. Architect → Designs high-level architecture (if major feature)
3. Software Design → Designs code structure and patterns
4. Developer → Implements code
5. Tester → Writes and runs tests (loops back if fails)
6. Auditor → Reviews for quality and security (loops back if fails)
7. Git → Commits and pushes
8. Documentation → Updates docs
9. Deployment → Deploys to environments
10. Project Manager → Marks task complete

### Quick Bug Fix Workflow
1. Debugger → Investigates root cause
2. Developer → Implements fix
3. Tester → Adds regression test
4. Auditor → Reviews fix
5. Git → Commits
6. Deployment → Deploys hotfix (if needed)

## Trigger Patterns

Claude automatically invokes appropriate agents based on these patterns:

| User Says | Agent Invoked | Rules Referenced |
|-----------|---------------|------------------|
| "commit", "push", "PR" | Git Agent | git-workflow |
| "implement", "write code", "fix bug" | Developer → Tester → Auditor → Git | architecture-design, code-review |
| "test", "run tests", "coverage" | Tester Agent | testing-qa |
| "review", "audit", "security check" | Auditor Agent | code-review, testing-qa |
| "document", "README", "API docs" | Documentation Agent | documentation |
| "create project", "track task" | Project Manager Agent | project-management |
| "design architecture", "ADR" | Architect Agent | architecture-design |
| "deploy", "release", "CI/CD" | Deployment Agent | deployment |
| "debug", "error", "not working" | Debugger Agent | (multiple) |
| "design pattern", "refactor" | Software Design Agent | architecture-design |
| "design a new project", "plan project" | Project Designer Agent | architecture-design, project-management |

## Best Practices

### Always ✅
- Reference relevant rules before taking action
- Use appropriate specialized agents
- Follow agent collaboration workflows
- Update project/task status as work progresses
- Document decisions and rationale
- Test code before committing
- Review code before merging

### Never ❌
- Skip referencing rules for standard operations
- Commit code without testing and review
- Skip agent workflows (Developer → Tester → Auditor → Git)
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
- Add new rules in `.claude/rules/`
- Create custom agents in `.claude/agents/`
- Modify existing agents and rules
- Add project-specific documentation
- Update `config.md` with your preferences

## Notes

- This is an Obsidian vault - use `[[wiki-links]]` and tags
- Rules contain best practices (auto-loaded each session)
- Agents are specialized personas for specific tasks
- Projects directory contains work-in-progress and completed projects
- Always check `[[config]]` for personal settings first
