# Vaulty - Context Memory System for Claude

A template repository designed to give Claude persistent context memory and specialized agent capabilities. This system enables Claude to maintain consistent workflows, reference best practices, and manage projects effectively across sessions.

## 🎯 What is Vaulty?

Vaulty is an **Obsidian vault** that serves as a comprehensive context memory system for Claude. It provides:

- **Memory Files**: Best practices and guidelines for different domains (git, testing, deployment, etc.)
- **Language-Specific Best Practices**: Detailed guides for 12 programming languages with idioms, patterns, and tooling
- **Specialized Agents**: Built-in subagent types Claude can delegate to via the Task tool
- **Project Management**: Structured system for tracking projects and tasks
- **Agent Collaboration**: Workflows where agents work together (Developer → Tester → Auditor → Git)

📊 **[View System Interaction Diagrams](INTERACTION-DIAGRAM.md)** - Visual guide to how agents collaborate

## 🔀 Using This Template Repository

Vaulty is designed as a **GitHub Template Repository** for personal use. Choose your path:

### 👤 For Personal Use (Recommended)

**This is the most common use case** - create your own copy to customize for your workflow:

1. **Click "Use this template"** button on GitHub (or [use this link](../../generate))
2. Choose a name for your repository (e.g., `my-vaulty`, `claude-memory`)
3. Select **Private** or **Public** (your choice)
4. Click "Create repository from template"
5. Clone your new repository and customize it

**Benefits:**
- ✅ Your own independent copy (no fork relationship)
- ✅ Customize agents, memory files, and config for your needs
- ✅ Add your personal projects without cluttering the template
- ✅ Pull updates from the template when you want them
- ✅ No expectation to contribute back (unless you want to!)

### 🤝 For Contributing to Vaulty Framework

**Want to improve Vaulty for everyone?** We welcome contributions!

1. **Fork this repository** (not "Use this template")
2. Make improvements to core framework files (agents, memory files, templates)
3. Submit a Pull Request
4. See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines

**What to contribute:**
- New memory files for domains (security, performance, etc.)
- New programming language guides
- Improved agent workflows
- Bug fixes and documentation
- Example projects and use cases

**What NOT to contribute:**
- Your personal `config.md` settings
- Your specific projects
- Workflow tweaks specific to your needs

### 🔄 Staying Updated

If you used the template and want to pull in new improvements:

```bash
# Add the original template as a remote (one time)
git remote add template https://github.com/rhwendt/vaulty.git

# Check what's new in the template
git fetch template
git show template/main:CHANGELOG.md

# Merge updates when ready
git merge template/main --allow-unrelated-histories

# Resolve conflicts - common areas:
# - .claude/rules/* (framework best practices)
# - .claude/agents/* (subagent definitions)
# - CLAUDE.md (main config file)
# Keep YOUR changes in:
# - config.md (your personal settings)
# - projects/* (your projects)
```

**What Gets Updated:**
- ✅ `.claude/rules/` - Best practices and guidelines
- ✅ `.claude/agents/` - Subagent definitions (official format)
- ✅ `CLAUDE.md` - Main documentation
- ✅ Templates and example projects
- ⚠️ You keep: `config.md`, `projects/`, custom rules you added

**Track updates:**
- 📋 View **[CHANGELOG.md](CHANGELOG.md)** for version history and breaking changes
- ⭐ Watch releases on GitHub for notifications
- 📌 Check [GitHub Releases](../../releases) for new versions

## 🚀 Quick Start

### 1. Clone This Repository

```bash
git clone https://github.com/yourusername/vaulty.git
cd vaulty
```

### 2. Open in Obsidian

1. Download [Obsidian](https://obsidian.md/)
2. Open Obsidian
3. Click "Open folder as vault"
4. Select the `vaulty` directory

### 3. Configure Your Personal Settings ⚙️

**IMPORTANT**: Create your personal config file before using Vaulty!

```bash
# Copy the template to create your config
cp config.template.md config.md

# Edit config.md with your preferences
# (use your favorite editor)
```

Your `config.md` contains personal settings like:
- Where you store your code repos locally (`repos_directory`)
- Your preferred programming languages and frameworks
- Your git preferences (default branch, commit style)
- Your code style preferences (indentation, line length)
- Your testing preferences (frameworks, coverage requirements)
- Your deployment setup (cloud provider, CI/CD platform)
- And much more!

**Why this matters**: Every agent and memory file references your config to personalize their behavior. Claude will use your preferred tools, follow your code style, and work with your local directory structure.

> [!TIP]
> Don't worry about filling out everything at once. Start with the basics (name, repos_directory, default_branch) and add more as needed. The template has detailed examples for every setting.

### 4. Start Using with Claude

When interacting with Claude in this repository:

**For Git Operations:**
```
"Commit these changes to git"
```
→ Claude should delegate to `git-helper` subagent, referencing `.claude/rules/git-workflow.md`

**For Development:**
```
"Write a Python script to process JSON files"
```
→ Claude should follow the workflow: `developer` → `tester` → `auditor` → `git-helper`

**For Project Management:**
```
"Create a new project for building a REST API"
```
→ Claude should delegate to `project-manager` subagent with project templates

## ⚙️ Environment Configuration

Vaulty agents work with any Claude model available to you. Control model selection and other Claude Code behavior via environment variables.

### Model Configuration

All subagents default to Sonnet unless you configure otherwise. To use different models:

```bash
# Set model for all subagents (overrides defaults)
export CLAUDE_CODE_SUBAGENT_MODEL="claude-opus-4-5-20251101"

# Or set main model (affects primary Claude Code thread)
export ANTHROPIC_MODEL="claude-opus-4-5-20251101"
```

**Recommended model strategy:**
- **High-thinking agents** (developer, tester, auditor, architect, software-designer, project-designer): Opus for complex reasoning
- **Operational agents** (git-helper, debugger, deployer, documenter, project-manager): Sonnet for efficiency

**Note:** Vaulty doesn't hardcode model requirements - it works with any Claude model available to you.

### Useful Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `CLAUDE_CODE_SUBAGENT_MODEL` | Override all subagent models | `export CLAUDE_CODE_SUBAGENT_MODEL="claude-opus-4-5-20251101"` |
| `ANTHROPIC_API_KEY` | API key (if using API instead of subscription) | `export ANTHROPIC_API_KEY="sk-..."` |
| `CLAUDE_ENV_FILE` | Shell script sourced before each bash command | `export CLAUDE_ENV_FILE="~/.claude-env.sh"` |
| `MCP_TIMEOUT` | MCP server startup timeout (milliseconds) | `export MCP_TIMEOUT="10000"` |
| `DEBUG` | Enable verbose debug logging | `export DEBUG=1` |
| `ANTHROPIC_LOG` | Anthropic SDK logging level | `export ANTHROPIC_LOG="debug"` |

### Setting Environment Variables

**Permanent configuration** (add to shell config):

For zsh (macOS/modern Linux):
```bash
# Add to ~/.zshrc
export CLAUDE_CODE_SUBAGENT_MODEL="claude-opus-4-5-20251101"
export CLAUDE_ENV_FILE="$HOME/.claude-env.sh"

# Reload: source ~/.zshrc
```

For bash:
```bash
# Add to ~/.bash_profile or ~/.bashrc
export CLAUDE_CODE_SUBAGENT_MODEL="claude-opus-4-5-20251101"
export CLAUDE_ENV_FILE="$HOME/.claude-env.sh"

# Reload: source ~/.bash_profile
```

**Environment persistence file** (recommended for project-specific settings):

Create `~/.claude-env.sh`:
```bash
#!/bin/bash
# Load environment for all Claude Code bash commands
export NODE_ENV=development
export DB_URL="postgresql://localhost/mydb"
source ~/.nvm/nvm.sh  # Load version managers
```

Then set: `export CLAUDE_ENV_FILE="$HOME/.claude-env.sh"`

### AWS Bedrock / Vertex AI

For enterprise users using AWS Bedrock or Google Vertex AI:

**AWS Bedrock:**
```bash
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1
# AWS credentials via AWS CLI config or environment variables
```

**Google Vertex AI:**
```bash
export CLAUDE_CODE_USE_VERTEX=1
export ANTHROPIC_VERTEX_PROJECT_ID=my-project-123
export CLOUD_ML_REGION=us-east3
# Authenticate: gcloud auth application-default login
```

See [official Claude Code documentation](https://code.claude.com/docs/en/settings) for complete environment variable reference.

## 📁 Repository Structure

```
vaulty/
├── CLAUDE.md                   # Main instructions for Claude (trigger words, workflows)
├── README.md                   # This file
├── .obsidian/                  # Obsidian configuration
│
├── config.template.md          # Template for your personal configuration
├── config.md                   # YOUR personal config (git-ignored, create from template)
│
├── .claude/                    # Claude Code configuration
│   ├── rules/                  # Best practices (auto-loaded)
│   │   ├── git-workflow.md     # Git operations and standards
│   │   ├── project-management.md # Project and task management
│   │   ├── documentation.md    # Documentation standards
│   │   ├── code-review.md      # Code review guidelines
│   │   ├── testing-qa.md       # Testing and QA practices
│   │   ├── deployment.md       # Deployment procedures
│   │   ├── communication.md    # Communication standards
│   │   ├── architecture-design.md # Architecture patterns and principles
│   │   └── languages/          # Language-specific best practices
│   │       ├── python.md       # Python idioms, PEP 8, type hints
│   │       ├── javascript.md   # JavaScript ES6+, async patterns
│   │       ├── typescript.md   # TypeScript types, strict mode
│   │       ├── go.md           # Go idioms, goroutines, channels
│   │       ├── rust.md         # Rust ownership, borrowing, traits
│   │       ├── java.md         # Java patterns, Spring Boot
│   │       ├── csharp.md       # C# async/await, LINQ, .NET
│   │       ├── cpp.md          # C++ modern features, RAII
│   │       ├── php.md          # PHP 8+, Laravel patterns
│   │       ├── ruby.md         # Ruby idioms, Rails patterns
│   │       ├── swift.md        # Swift optionals, protocols
│   │       └── kotlin.md       # Kotlin null safety, coroutines
│   │
│   └── agents/                 # Agent instruction files (read before delegating)
│       ├── git-helper.md       # Git operations specialist
│       ├── developer.md        # Code development specialist
│       ├── tester.md           # Testing and QA specialist
│       ├── auditor.md          # Code review and security specialist
│       ├── documenter.md       # Documentation specialist
│       ├── project-manager.md  # Project management specialist
│       ├── project-designer.md # New project planning and design specialist
│       ├── architect.md        # Architecture and design specialist
│       ├── deployer.md         # Deployment and DevOps specialist
│       ├── debugger.md         # Debugging and troubleshooting specialist
│       └── software-designer.md # Design patterns specialist
│
└── projects/                   # Your projects go here
    ├── _templates/             # Templates for new projects and tasks
    │   ├── project-overview-template.md
    │   └── task-template.md
    │
    └── example-project/        # Example project structure
        ├── overview.md         # Project overview and status
        └── tasks/              # Individual task files
            ├── task-001-implement-auth.md
            └── task-002-build-api.md
```

## 🤖 Specialized Agents

Claude Code has built-in subagent types that can be delegated to using the Task tool. The `.claude/agents/` files provide instructions for Claude to read before delegating:

### Development Workflow Agents

| Agent (subagent_type) | Purpose | Use When User Says |
|----------------------|---------|-------------------|
| **developer** | Write and modify code | "write", "implement", "create script", "fix bug" |
| **tester** | Write and run tests | "test", "write tests", "check coverage" |
| **auditor** | Review code for quality/security | "review", "audit", "check for issues" |
| **software-designer** | Apply design patterns | "design pattern", "refactor", "improve design" |

### Operations Agents

| Agent (subagent_type) | Purpose | Use When User Says |
|----------------------|---------|-------------------|
| **git-helper** | Version control operations | "commit", "push", "pull request", "merge" |
| **deployer** | Deploy and release | "deploy", "release", "rollback", "CI/CD" |
| **debugger** | Investigate issues | "debug", "error", "bug", "not working" |

### Planning Agents

| Agent (subagent_type) | Purpose | Use When User Says |
|----------------------|---------|-------------------|
| **project-designer** | Design new projects from requirements | "help me design a project", "I want to build", "plan a project" |
| **project-manager** | Manage projects and tasks | "create project", "track task", "status" |
| **architect** | High-level design decisions | "architecture", "design system", "ADR" |
| **documenter** | Create/update docs | "document", "README", "write docs" |

## 🔄 Agent Collaboration Workflows

These workflows show the recommended delegation sequence. Claude must explicitly delegate using the Task tool with each subagent_type.

### New Project Design (From Scratch)

When user asks to design a new project, Claude should delegate in this order:

```
1. project-designer → Gathers requirements and asks clarifying questions
   ↓
2. project-designer → Explores technology options and trade-offs
   ↓
3. project-designer → Designs architecture and creates roadmap
   ↓
4. architect → Creates ADRs for key technology decisions
   ↓
5. project-manager → Sets up Vaulty project structure and tasks
   ↓
6. developer → Ready to begin implementation

Example: "I want to build a task management API with real-time updates"
→ Claude delegates to project-designer to gather requirements
→ Explores options (WebSockets vs SSE, PostgreSQL vs MongoDB)
→ Creates architecture diagram and implementation roadmap
→ Delegates to other agents for execution
```

### Complete Feature Development

When user asks to implement a feature, Claude should delegate in this order:

```
1. project-manager → Creates/tracks task
2. architect → Designs high-level architecture (if major)
3. software-designer → Designs code structure
4. developer → Implements code
5. tester → Writes and runs tests
   ↓ (if tests fail, back to developer)
6. auditor → Reviews for quality and security
   ↓ (if review fails, back to developer)
7. git-helper → Commits and pushes code
8. documenter → Updates docs
9. deployer → Deploys to environments
10. project-manager → Marks task complete
```

### Quick Bug Fix

```
1. debugger → Investigates and finds root cause
2. developer → Implements fix
3. tester → Adds regression test
4. auditor → Reviews fix
5. git-helper → Commits fix
6. deployer → Deploys hotfix
```

## 📝 Rules Files (Best Practices)

Rules files in `.claude/rules/` contain best practices that agents reference:

- **git-workflow.md**: How to commit, branch, create PRs
- **testing-qa.md**: Testing standards, coverage requirements
- **code-review.md**: Code review checklist, security concerns
- **deployment.md**: Deployment procedures, rollback plans
- **architecture-design.md**: Design patterns, SOLID principles
- **project-management.md**: Task tracking, project organization
- **documentation.md**: Documentation standards
- **communication.md**: Communication templates

### 🔤 Language-Specific Best Practices

Each language has its own comprehensive guide in `.claude/rules/languages/`:

| Language | Key Topics |
|----------|------------|
| **Python** | PEP 8, type hints, pytest, dataclasses, async/await |
| **JavaScript** | ES6+, async/promises, Node.js, Jest, modern patterns |
| **TypeScript** | Type system, strict mode, generics, decorators |
| **Go** | Goroutines, channels, error handling, interfaces, table tests |
| **Rust** | Ownership, borrowing, Result/Option, traits, cargo |
| **Java** | Streams, Optional, Spring Boot, JUnit 5, modern features |
| **C#** | Async/await, LINQ, nullable types, xUnit, .NET patterns |
| **C++** | Modern C++, RAII, smart pointers, move semantics |
| **PHP** | PHP 8+, Laravel, type safety, PSR standards, PHPUnit |
| **Ruby** | Ruby idioms, Rails, RSpec, metaprogramming |
| **Swift** | Optionals, protocols, SwiftUI, value types, XCTest |
| **Kotlin** | Null safety, coroutines, sealed classes, data classes |

**What's included in each language guide:**
- ✅ Language philosophy and idioms
- ✅ Naming conventions and code style
- ✅ Error handling patterns
- ✅ Testing best practices
- ✅ Framework-specific patterns
- ✅ Linting and formatting tools
- ✅ Common anti-patterns to avoid
- ✅ Performance tips
- ✅ Official resources and style guides

## 🎯 Usage Examples

### Example 1: Start a New Project

```
You: "Create a new project for building a task management API"

Claude should:
1. Read .claude/agents/project-manager-agent.md for context
2. Delegate to project-manager subagent with prompt including:
   - User's request
   - Reference to projects/_templates/
   - Reference to .claude/rules/project-management.md
3. Create projects/task-management-api/
4. Create overview.md from template
5. Create initial task breakdown
```

### Example 2: Implement a Feature

```
You: "Write a Python function to validate email addresses"

Claude should delegate in sequence:
1. developer → Write the function (include .claude/rules/architecture-design.md in prompt)
2. tester → Write tests (include .claude/rules/testing-qa.md in prompt)
3. auditor → Review code (include .claude/rules/code-review.md in prompt)
4. git-helper → Commit changes (include .claude/rules/git-workflow.md in prompt)
```

### Example 3: Deploy Changes

```
You: "Deploy this to production"

Claude should:
1. Read .claude/agents/deployment-agent.md for context
2. Delegate to deployer subagent with prompt including:
   - Reference to .claude/rules/deployment.md
   - Pre-deployment checklist from rules
3. Execute deployment steps
4. Report status
```

## 🔧 Customization

### Adding New Rules Files

Create a new file in `.claude/rules/` for your domain:

```markdown
# Your Domain Best Practices

## When to Reference This File
[Explain when agents should use this]

## Best Practices
...

## Examples
...
```

### Adding Agent Instruction Files

The `.claude/agents/` directory contains instruction files that Claude reads before delegating to a built-in subagent type. These provide domain-specific context and guidelines.

**Important**: These files do NOT create new subagent types. Claude Code has a fixed set of built-in subagent types (`developer`, `tester`, `auditor`, `git-helper`, `debugger`, `deployer`, `documenter`, `architect`, `software-designer`, `project-manager`, `project-designer`, `Explore`, `Plan`).

Create instruction files following this format:

```markdown
---
name: agent-name
description: When to use this agent (for human reference)
tools: Read, Write, Edit, Bash, Glob, Grep
---

# Agent Title

## Role
What this agent focuses on...

## Before Starting
- Check config.md for user preferences
- Reference relevant `.claude/rules/` files

## Guidelines
1. Step one
2. Step two
...
```

Claude should read the relevant instruction file before calling the Task tool to include context in the delegation prompt.

### Creating Projects

Use the templates in `projects/_templates/`:

```bash
# Create new project structure
mkdir -p projects/my-new-project/tasks

# Copy templates
cp projects/_templates/project-overview-template.md projects/my-new-project/overview.md
```

## 🌟 Benefits

### For Individual Developers
- ✅ Consistent git workflow across projects
- ✅ Always-applied code review standards
- ✅ Systematic testing approach
- ✅ Structured project management
- ✅ Best practices at your fingertips

### For Teams
- ✅ Shared best practices and standards
- ✅ Consistent code quality
- ✅ Onboarding documentation
- ✅ Collaborative decision records (ADRs)
- ✅ Standardized workflows

### For Claude
- ✅ Persistent context across sessions
- ✅ Domain expertise on demand
- ✅ Systematic problem-solving workflows
- ✅ Quality gates (testing, review) before commits
- ✅ Collaborative agent patterns

## 📋 Best Practices

### ✅ Do

- Reference memory files before operations
- Use appropriate agents for tasks
- Follow agent collaboration workflows
- Update project/task status regularly
- Document decisions in ADRs
- Test before committing
- Review before merging

### ❌ Don't

- Skip agent workflows (especially Developer → Tester → Auditor)
- Commit without testing
- Deploy without consulting deployment checklist
- Make architectural decisions without documentation
- Skip referencing memory files

## 🔗 Obsidian Features

This vault uses Obsidian-specific features:

### Wiki Links
```markdown
Link to other files: [[memory/git-workflow]]
Link to sections: [[project-overview#goals]]
```

### Tags
```markdown
Organize with tags: #task #status/in-progress #priority/high
```

### Callouts
```markdown
> [!NOTE]
> Important information here

> [!WARNING]
> Caution: Pay attention to this
```

## 🔌 Recommended Obsidian Plugins & Settings

Enhance your Vaulty experience with these Obsidian plugins and settings. All are **optional** but highly recommended for power users.

### Essential Plugins

#### **Dataview** (Highly Recommended)
Query and display data from your vault dynamically.

**Use cases for Vaulty:**
- List all agents with their trigger patterns
- Show all projects with their current status
- Display tasks by priority or status
- Generate agent usage reports

**Example query:**
```dataview
TABLE role, trigger-patterns
FROM "agents"
WHERE contains(file.tags, "#agent")
```

**Install:** Settings → Community Plugins → Browse → Search "Dataview"

#### **Templater** (Recommended)
More powerful templating than core Templates plugin.

**Use cases for Vaulty:**
- Auto-generate project names with dates
- Insert current task count
- Dynamic agent invocations
- Auto-fill project metadata

**Install:** Settings → Community Plugins → Browse → Search "Templater"

#### **Tag Wrangler** (Recommended)
Better tag management and navigation.

**Use cases for Vaulty:**
- Quickly rename tags across all files (e.g., `#status/todo` → `#status/pending`)
- View tag hierarchy for agent categories
- Search and filter by nested tags
- Clean up unused tags

**Install:** Settings → Community Plugins → Browse → Search "Tag Wrangler"

### Project Management Plugins

#### **Tasks**
Advanced task management with queries and filters.

**Use cases for Vaulty:**
- Track TODOs across all project files
- Filter tasks by due date, priority, status
- Generate task reports per project
- Recurring tasks for maintenance work

**Install:** Settings → Community Plugins → Browse → Search "Tasks"

#### **Kanban**
Visual kanban board for project management.

**Use cases for Vaulty:**
- Visualize project task flow (Todo → In Progress → Done)
- Drag-and-drop task management
- Board per project or one global board
- Alternative to markdown task lists

**Install:** Settings → Community Plugins → Browse → Search "Kanban"

### Navigation Plugins

#### **Recent Files**
Quick access to recently edited files.

**Use cases for Vaulty:**
- Quickly return to agents you're actively working with
- Access recently referenced memory files
- Navigate between project files efficiently

**Install:** Settings → Community Plugins → Browse → Search "Recent Files"

#### **Quick Switcher++**
Enhanced file switcher with symbols and headings.

**Use cases for Vaulty:**
- Jump to specific agent sections quickly
- Search within memory file headings
- Navigate to project task sections

**Install:** Settings → Community Plugins → Browse → Search "Quick Switcher++"

### Visualization Plugins

#### **Excalidraw**
Create diagrams and sketches directly in Obsidian.

**Use cases for Vaulty:**
- Extend interaction diagrams with custom flows
- Sketch architecture designs before implementing
- Visual project planning
- Whiteboard with Claude for complex planning

**Install:** Settings → Community Plugins → Browse → Search "Excalidraw"

#### **Graph Analysis**
Enhanced graph view with better filtering.

**Use cases for Vaulty:**
- Visualize connections between agents and memory files
- Identify orphaned files
- Understand project dependencies
- See agent collaboration patterns

**Install:** Settings → Community Plugins → Browse → Search "Graph Analysis"

### Recommended Core Settings

Enable these built-in Obsidian features (already configured in this template):

#### **Graph View** ✅ Enabled
- View connections between agents and memory files
- Settings → Graph View → Enable filters by tag
- Useful for understanding agent relationships

#### **Templates** ✅ Enabled
- Templates folder: `projects/_templates/`
- Use for creating new projects and tasks
- Settings → Templates → Template folder location

#### **Tag Pane** ✅ Enabled
- View all tags in vault (#agent, #memory, #task)
- Click tags to see all matching files
- Useful for navigating agent categories

#### **Daily Notes** ✅ Enabled (Optional Use)
- Could be used for session logs with Claude
- Track what agents were used each day
- Document decisions and outcomes

#### **Canvas** ✅ Enabled
- Create visual boards connecting agents, memory files, and projects
- Alternative to text-based project planning
- Drag and link related files visually

### Optimal Settings Configuration

**File Explorer Settings:**
```
Settings → Files and Links
✅ Automatically update internal links
✅ Default location for new notes: Same folder as current file
✅ New link format: Relative path to file
```

**Editor Settings:**
```
Settings → Editor
✅ Show line numbers (helpful when working with Claude)
✅ Readable line length (easier to read long memory files)
✅ Strict line breaks (markdown compatibility)
```

**Appearance Settings:**
```
Settings → Appearance
- Enable/disable: Your preference
- Recommended: Use a theme that supports callouts and tags well
- Popular themes: Minimal, Things, California Coast
```

### Quick Start: Installing Plugins

1. **Enable Community Plugins**
   - Settings → Community Plugins → Turn off Restricted Mode
   - Click "Browse" to search for plugins

2. **Install Essential Plugins**
   - Start with: Dataview, Tag Wrangler, Recent Files
   - Add others as needed based on your workflow

3. **Configure Plugin Settings**
   - Dataview: Enable JavaScript queries (optional)
   - Templater: Set template folder to `projects/_templates/`
   - Tasks: Configure global filter if needed

### Plugin Maintenance

**Keep plugins updated:**
- Settings → Community Plugins → Check for updates regularly
- Updates often include bug fixes and new features

**Disable unused plugins:**
- Improves vault performance
- Reduces potential conflicts

## 🤝 Contributing

This is a **template repository** - feel free to use it for personal projects!

**Want to contribute to the Vaulty framework itself?** We welcome:
- Additional memory files for other domains
- New programming language guides
- Custom agents for specialized tasks
- Improved templates and examples
- Documentation improvements
- Bug fixes

See **[CONTRIBUTING.md](CONTRIBUTING.md)** for detailed contribution guidelines.

## 📄 License

MIT License - feel free to use this template for your own projects!

## 🎓 Learn More

- [Obsidian Documentation](https://help.obsidian.md/)
- [Claude Documentation](https://docs.anthropic.com/)
- For questions or issues with this template, open an issue on GitHub

## 💡 Tips

1. **Start Small**: Begin with one project and gradually adopt the full system
2. **Customize**: Adapt memory files and agents to your workflow
3. **Be Consistent**: Use the system regularly for best results
4. **Review Regularly**: Update memory files as you learn new best practices
5. **Share**: Use as a team template for consistent standards

---

**Made for Claude** | **Template Repository** | **Obsidian Vault**
