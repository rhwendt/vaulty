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

# Resolve any conflicts (usually just in framework files)
```

**Track updates:**
- 📋 View **[CHANGELOG.md](CHANGELOG.md)** for version history
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
→ Claude will use the Git Agent and reference `memory/git-workflow.md`

**For Development:**
```
"Write a Python script to process JSON files"
```
→ Claude will use Developer Agent → Tester Agent → Auditor Agent → Git Agent workflow

**For Project Management:**
```
"Create a new project for building a REST API"
```
→ Claude will use Project Manager Agent and create structured project files

## 📁 Repository Structure

```
vaulty/
├── .claude.md                  # Main instructions for Claude (trigger words, workflows)
├── README.md                   # This file
├── .obsidian/                  # Obsidian configuration
│
├── config.template.md          # Template for your personal configuration
├── config.md                   # YOUR personal config (git-ignored, create from template)
│
├── memory/                     # Context memory files (best practices)
│   ├── git-workflow.md         # Git operations and standards
│   ├── project-management.md   # Project and task management
│   ├── documentation.md        # Documentation standards
│   ├── code-review.md          # Code review guidelines
│   ├── testing-qa.md           # Testing and QA practices
│   ├── deployment.md           # Deployment procedures
│   ├── communication.md        # Communication standards
│   ├── architecture-design.md  # Architecture patterns and principles
│   └── languages/              # Language-specific best practices
│       ├── python.md           # Python idioms, PEP 8, type hints
│       ├── javascript.md       # JavaScript ES6+, async patterns
│       ├── typescript.md       # TypeScript types, strict mode
│       ├── go.md               # Go idioms, goroutines, channels
│       ├── rust.md             # Rust ownership, borrowing, traits
│       ├── java.md             # Java patterns, Spring Boot
│       ├── csharp.md           # C# async/await, LINQ, .NET
│       ├── cpp.md              # C++ modern features, RAII
│       ├── php.md              # PHP 8+, Laravel patterns
│       ├── ruby.md             # Ruby idioms, Rails patterns
│       ├── swift.md            # Swift optionals, protocols
│       └── kotlin.md           # Kotlin null safety, coroutines
│
├── agents/                     # Specialized agent prompts
│   ├── git-agent.md            # Git operations specialist
│   ├── developer-agent.md      # Code development specialist
│   ├── tester-agent.md         # Testing and QA specialist
│   ├── auditor-agent.md        # Code review and security specialist
│   ├── documentation-agent.md  # Documentation specialist
│   ├── project-manager-agent.md # Project management specialist
│   ├── architect-agent.md      # Architecture and design specialist
│   ├── deployment-agent.md     # Deployment and DevOps specialist
│   ├── debugger-agent.md       # Debugging and troubleshooting specialist
│   └── software-design-agent.md # Design patterns specialist
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

Claude will automatically invoke specialized agents based on trigger words:

### Development Workflow Agents

| Agent | Purpose | Trigger Words |
|-------|---------|---------------|
| **Developer** | Write and modify code | "write", "implement", "create script", "fix bug" |
| **Tester** | Write and run tests | "test", "write tests", "check coverage" |
| **Auditor** | Review code for quality/security | "review", "audit", "check for issues" |
| **Software Design** | Apply design patterns | "design pattern", "refactor", "improve design" |

### Operations Agents

| Agent | Purpose | Trigger Words |
|-------|---------|---------------|
| **Git** | Version control operations | "commit", "push", "pull request", "merge" |
| **Deployment** | Deploy and release | "deploy", "release", "rollback", "CI/CD" |
| **Debugger** | Investigate issues | "debug", "error", "bug", "not working" |

### Planning Agents

| Agent | Purpose | Trigger Words |
|-------|---------|---------------|
| **Project Manager** | Manage projects and tasks | "create project", "track task", "status" |
| **Architect** | High-level design decisions | "architecture", "design system", "ADR" |
| **Documentation** | Create/update docs | "document", "README", "write docs" |

## 🔄 Agent Collaboration Workflows

### Complete Feature Development

When you ask Claude to implement a feature, agents collaborate:

```
1. Project Manager → Creates/tracks task
2. Architect → Designs high-level architecture (if major)
3. Software Design → Designs code structure
4. Developer → Implements code
5. Tester → Writes and runs tests
   ↓ (if tests fail, back to Developer)
6. Auditor → Reviews for quality and security
   ↓ (if review fails, back to Developer)
7. Git → Commits and pushes code
8. Documentation → Updates docs
9. Deployment → Deploys to environments
10. Project Manager → Marks task complete
```

### Quick Bug Fix

```
1. Debugger → Investigates and finds root cause
2. Developer → Implements fix
3. Tester → Adds regression test
4. Auditor → Reviews fix
5. Git → Commits fix
6. Deployment → Deploys hotfix
```

## 📝 Memory Files

Memory files contain best practices that agents reference:

- **git-workflow.md**: How to commit, branch, create PRs
- **testing-qa.md**: Testing standards, coverage requirements
- **code-review.md**: Code review checklist, security concerns
- **deployment.md**: Deployment procedures, rollback plans
- **architecture-design.md**: Design patterns, SOLID principles
- **project-management.md**: Task tracking, project organization
- **documentation.md**: Documentation standards
- **communication.md**: Communication templates

### 🔤 Language-Specific Best Practices

Each language has its own comprehensive guide in `memory/languages/`:

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

Claude will:
1. Use Project Manager Agent
2. Create projects/task-management-api/
3. Create overview.md from template
4. Create initial task breakdown
5. Reference memory/project-management.md for structure
```

### Example 2: Implement a Feature

```
You: "Write a Python function to validate email addresses"

Claude will:
1. Use Developer Agent (references memory/architecture-design.md)
2. Write the function with proper error handling
3. Use Tester Agent (references memory/testing-qa.md)
4. Write comprehensive tests
5. Use Auditor Agent (references memory/code-review.md)
6. Review for security and quality
7. Use Git Agent (references memory/git-workflow.md)
8. Commit with proper message
```

### Example 3: Deploy Changes

```
You: "Deploy this to production"

Claude will:
1. Use Deployment Agent
2. Reference memory/deployment.md
3. Run pre-deployment checklist
4. Execute deployment steps
5. Monitor deployment
6. Report status
```

## 🔧 Customization

### Adding New Memory Files

Create a new file in `memory/` for your domain:

```markdown
#memory/your-domain

# Your Domain Best Practices

## When to Use
...

## Best Practices
...
```

### Adding Custom Agents

Create a new agent in `agents/`:

```markdown
#agent #your-specialty

# Your Custom Agent

## Role
What this agent does...

## Key Memory Files
- [[memory/relevant-file]]

## Trigger Patterns
- "trigger word 1"
- "trigger word 2"
```

Then update `.claude.md` to include your new agent's trigger patterns.

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
