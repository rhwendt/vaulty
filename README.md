# Vaulty - Context Memory System for Claude

A template repository designed to give Claude persistent context memory and specialized agent capabilities. This system enables Claude to maintain consistent workflows, reference best practices, and manage projects effectively across sessions.

## 🎯 What is Vaulty?

Vaulty is an **Obsidian vault** that serves as a comprehensive context memory system for Claude. It provides:

- **Memory Files**: Best practices and guidelines for different domains (git, testing, deployment, etc.)
- **Language-Specific Best Practices**: Detailed guides for 12 programming languages with idioms, patterns, and tooling
- **Specialized Agents**: Expert personas that Claude can invoke for specific tasks
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
# - .claude/CLAUDE.md (main project instructions)
# Keep YOUR changes in:
# - config.md (your personal settings)
# - projects/* (your projects)
```

**What Gets Updated:**
- ✅ `.claude/rules/` - Best practices and guidelines
- ✅ `.claude/agents/` - Subagent definitions
- ✅ `.claude/CLAUDE.md` - Main project instructions
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

### 4. Start Using with Claude Code

Run Claude Code in this directory. The rules in `.claude/rules/` and agents in `.claude/agents/` are automatically loaded and provide context for your interactions.

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
├── README.md                   # This file
├── .obsidian/                  # Obsidian configuration
│
├── config.template.md          # Template for your personal configuration
├── config.md                   # YOUR personal config (git-ignored, create from template)
│
├── .claude/                    # Claude Code configuration
│   ├── CLAUDE.md               # Main project instructions (concise)
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
│   └── agents/                 # Specialized subagents (YAML frontmatter)
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

Vaulty includes specialized subagents in `.claude/agents/` that Claude automatically delegates to based on task context:

- **Developer** - Code implementation and bug fixes
- **Tester** - Writing and running tests
- **Auditor** - Code review and security checks
- **Software Designer** - Design patterns and refactoring
- **Git Helper** - Version control operations
- **Deployer** - Deployment and CI/CD
- **Debugger** - Investigating issues
- **Project Designer** - New project planning
- **Project Manager** - Task and project tracking
- **Architect** - High-level design decisions
- **Documenter** - Documentation creation

Agents and their collaboration workflows are defined in `.claude/agents/` and `.claude/rules/workflows.md`.

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

### Adding Custom Subagents

Create a new subagent in `.claude/agents/` using the official format with YAML frontmatter:

```markdown
---
name: your-specialty
model: claude-sonnet-4-5-20250929
description: "Specialized agent for [specific task]. Triggered when user requests [specific actions]."
---

# Your Custom Agent

## Role
What this agent does...

## Rules Referenced
- `.claude/rules/relevant-file.md`

## Workflow Steps
1. Step one
2. Step two
...
```

Subagents are automatically delegated based on their `description` field in the YAML frontmatter.

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
