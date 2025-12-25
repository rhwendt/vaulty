# Contributing to Vaulty

Thank you for your interest in Vaulty! This guide explains how to contribute to the **Vaulty framework** itself, as opposed to using it for your personal projects.

## 🎯 What is Vaulty?

Vaulty is a **template repository** designed for personal use. Users create their own copies to customize for their workflows. This creates two distinct types of contributions:

### ✅ Framework Contributions (Welcome!)

These improve Vaulty for everyone:

- **New or improved subagents** (`.claude/agents/`)
- **New or improved rules files** (`.claude/rules/`)
- **Language-specific best practices** (`.claude/rules/languages/`)
- **Template improvements** (`projects/_templates/`)
- **Documentation enhancements**
- **Bug fixes in framework logic**
- **New features for `CLAUDE.md`**
- **Interaction diagrams and examples**

### ❌ Personal Usage (Not for PRs)

These are specific to individual users:

- Your personal `config.md` file
- Your projects in `projects/`
- Your custom agents for your workflow
- Your specific memory file tweaks

## 🔀 Two Ways to Use Vaulty

### For Personal Use (Most Common)

1. **Click "Use this template"** on GitHub
2. Create your own repository (private or public)
3. Customize `config.md`, add your projects, tweak agents
4. Use it with Claude for your work
5. **No need to contribute back** unless you improve the framework

### For Contributing to Framework

1. **Fork this repository** (not "Use this template")
2. Make improvements to core framework files
3. Test your changes
4. Submit a Pull Request
5. Explain how your changes benefit all Vaulty users

## 🚀 How to Contribute

### 1. Find Something to Improve

**Good contribution ideas:**
- Add a new programming language to `.claude/rules/languages/`
- Improve an existing subagent with better workflows
- Add a new specialized subagent (e.g., "security.md" in `.claude/agents/`)
- Fix typos or improve documentation
- Add examples to README
- Improve templates in `projects/_templates/`
- Add new rules files for domains (e.g., `.claude/rules/api-design.md`)

**Check existing issues:**
- Look for issues tagged `good-first-issue`
- Look for `enhancement` or `documentation` tags

### 2. Make Your Changes

```bash
# Fork the repo on GitHub, then clone your fork
git clone https://github.com/YOUR-USERNAME/vaulty.git
cd vaulty

# Create a feature branch
git checkout -b feature/your-improvement

# Make your changes
# Test with Claude to ensure it works

# Commit with clear messages
git add .
git commit -m "feat: Add rules file for API design best practices"

# Push to your fork
git push origin feature/your-improvement
```

### 3. Submit a Pull Request

**In your PR description, include:**
- **What**: What did you add/change/fix?
- **Why**: Why is this useful for Vaulty users?
- **How**: How does it work?
- **Testing**: How did you test it with Claude?

**Example PR description:**
```markdown
## What
Added a new rules file for API design best practices (`.claude/rules/api-design.md`)

## Why
Many developers use Vaulty for API development projects. This rules file provides REST/GraphQL design principles, versioning strategies, and documentation standards.

## How
- Follows the same structure as other rules files
- References industry standards (REST, OpenAPI, GraphQL)
- Includes practical examples
- Can be referenced by Developer and Architect subagents

## Testing
- Tested with Claude on API design tasks
- Verified Claude references the file appropriately
- Checked integration with existing subagents
```

### 4. Code Review

- Maintainers will review your PR
- Address any feedback or requested changes
- Once approved, your contribution will be merged!

## 📝 Contribution Guidelines

### For New Rules Files

**Structure:**
```markdown
# Domain Name Best Practices

## When to Reference This File
[Explain when agents should use this]

## Core Principles
[List key principles]

## Best Practices
[Detailed best practices]

## Common Pitfalls
[What to avoid]

## Tools and Resources
[Relevant tools, links to docs]

## Examples
[Practical examples]
```

**Requirements:**
- Use Obsidian wiki links: `[[other-file]]` (optional)
- Clear, actionable guidance
- Industry-standard best practices
- Include examples where helpful
- Place in `.claude/rules/` directory

### For New Subagents

**Structure (with YAML frontmatter):**
```markdown
---
name: specialty-name
model: claude-sonnet-4-5-20250929
description: "Specialized agent for [specific task]. Triggered when user requests [specific actions]."
---

# Agent Name

## Role
[Clear description of what this agent does]

## Rules Referenced
- `.claude/rules/relevant-file-1.md`
- `.claude/rules/relevant-file-2.md`

## Workflow Steps
[Step-by-step process the agent follows]

## Example Usage
[Example of how Claude uses this agent]

## Collaboration Points
[Which other agents this works with]
```

**Requirements:**
- Use official Claude Code subagent format with YAML frontmatter
- Clear `description` field (used for automatic delegation)
- Specify appropriate `model` (opus for complex tasks, sonnet for operations)
- List relevant rules files to reference
- Include workflow steps
- Show how it integrates with other subagents
- Place in `.claude/agents/` directory

### For Language-Specific Files

Follow the existing pattern in `.claude/rules/languages/`:

**Must include:**
- ✅ Language philosophy and idioms
- ✅ Naming conventions and code style
- ✅ Error handling patterns
- ✅ Testing best practices
- ✅ Framework-specific patterns (if applicable)
- ✅ Linting and formatting tools
- ✅ Common anti-patterns to avoid
- ✅ Performance considerations
- ✅ Links to official style guides

### For Documentation

- Use clear, concise language
- Include examples where helpful
- Use proper Markdown formatting
- Test that wiki links work in Obsidian
- Check spelling and grammar

### For Issue Templates

- Keep them focused and actionable
- Include clear sections
- Make it easy for users to provide needed info

## 🐛 Reporting Bugs

**Before submitting:**
1. Check existing issues for duplicates
2. Verify it's a framework bug, not a personal config issue
3. Test with a fresh template copy if possible

**Use the Bug Report template and include:**
- What you expected to happen
- What actually happened
- Steps to reproduce
- Your environment (Obsidian version, Claude interface)
- Relevant files (agents, memory files involved)

## 💡 Requesting Features

**Use the Feature Request template and include:**
- Clear description of the feature
- Why it would benefit Vaulty users (not just you)
- How it might work
- Examples of similar features elsewhere (if applicable)

## ✅ Pull Request Checklist

Before submitting your PR:

- [ ] Changes benefit the framework, not just personal use
- [ ] Follows existing file structure and naming conventions
- [ ] Includes proper Obsidian tags and wiki links
- [ ] Documentation is clear and includes examples
- [ ] Tested with Claude to verify it works
- [ ] No personal config or project files included
- [ ] Commit messages are clear and descriptive
- [ ] PR description explains what, why, and how

## 🏷️ Versioning and Releases

**For Maintainers**: Vaulty uses [Semantic Versioning](https://semver.org/) (MAJOR.MINOR.PATCH)

### When to Bump Versions

**MAJOR (1.x.x)** - Breaking changes:
- Restructuring core directories (`.claude/agents/`, `.claude/rules/`, `projects/`)
- Incompatible changes to config.md format
- Removing or renaming core subagents
- Changes that require users to modify their setup

**MINOR (x.1.x)** - New features:
- New subagents
- New rules files
- New language guides
- New templates or examples
- Enhanced subagent capabilities

**PATCH (x.x.1)** - Bug fixes and improvements:
- Typo fixes
- Documentation improvements
- Bug fixes in subagent logic
- Clarifications to rules files
- Broken link fixes

### Release Process

1. **Update CHANGELOG.md**
   - Move items from `[Unreleased]` to new version section
   - Add release date
   - Follow Keep a Changelog format

2. **Create Git Tag**
   ```bash
   git tag -a v1.2.0 -m "Release v1.2.0: Add security subagent and API design rules"
   git push origin v1.2.0
   ```

3. **Create GitHub Release**
   - Go to Releases → Draft a new release
   - Select the tag you just created
   - Copy relevant section from CHANGELOG.md
   - Highlight key features/changes
   - Publish release

4. **Notify Users** (optional)
   - Post in Discussions
   - Users watching releases will be notified automatically

### CHANGELOG Format

```markdown
## [1.2.0] - 2025-01-15

### Added
- New Security subagent for security audits
- New rules file for API design best practices

### Changed
- Improved Git Helper workflow for better commit messages

### Fixed
- Fixed broken references in Developer subagent

### Deprecated
- Old project template (replaced with improved version)
```

## 🤝 Code of Conduct

- Be respectful and constructive
- Welcome newcomers and help them contribute
- Focus on making Vaulty better for everyone
- Assume good intent in discussions
- Give credit where credit is due

## 📜 License

By contributing to Vaulty, you agree that your contributions will be licensed under the MIT License.

## ❓ Questions?

- **Framework questions**: Open a discussion or issue
- **Personal usage questions**: Check the README first
- **Not sure if it's a contribution**: Open an issue to discuss!

---

**Thank you for helping make Vaulty better for everyone!** 🎉
