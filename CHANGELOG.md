# Changelog

All notable changes to the Vaulty framework will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Template repository documentation and contribution guidelines
- GitHub issue templates for bug reports and feature requests
- Versioning strategy with CHANGELOG
- Comprehensive Obsidian plugins and settings section in README
  - Essential plugins (Dataview, Templater, Tag Wrangler)
  - Project management plugins (Tasks, Kanban)
  - Navigation plugins (Recent Files, Quick Switcher++)
  - Visualization plugins (Excalidraw, Graph Analysis)
  - Recommended core settings configuration
  - Plugin installation and maintenance guide

## [1.0.0] - 2025-01-XX

### Added
- Initial release of Vaulty framework
- Core agent system with 10 specialized agents:
  - Developer Agent for code implementation
  - Tester Agent for testing and QA
  - Auditor Agent for code review and security
  - Git Agent for version control operations
  - Project Manager Agent for project tracking
  - Architect Agent for high-level design
  - Documentation Agent for documentation
  - Deployment Agent for DevOps tasks
  - Debugger Agent for troubleshooting
  - Software Design Agent for design patterns

- Memory files for best practices:
  - Git workflow and standards
  - Testing and QA practices
  - Code review guidelines
  - Deployment procedures
  - Architecture and design patterns
  - Project management system
  - Documentation standards
  - Communication templates

- Language-specific best practices for 12 languages:
  - Python, JavaScript, TypeScript
  - Go, Rust, Java
  - C#, C++, PHP
  - Ruby, Swift, Kotlin

- Project management system:
  - Project overview templates
  - Task tracking templates
  - Example project structure

- Obsidian integration:
  - Wiki links between files
  - Tag-based organization
  - Graph view support

- Configuration system:
  - Personal config template
  - Git-ignored user settings
  - Customizable preferences

- System interaction diagrams showing agent collaboration workflows

### Documentation
- Comprehensive README with quick start guide
- CONTRIBUTING.md with contribution guidelines
- Issue templates for bugs and features
- Template repository usage instructions

---

## Version Strategy

Vaulty uses [Semantic Versioning](https://semver.org/):

**MAJOR.MINOR.PATCH** (e.g., 1.2.3)

- **MAJOR** (1.x.x): Breaking changes to core framework structure
  - Example: Restructuring agents/ or memory/ directories
  - Example: Changing config.md format in incompatible ways

- **MINOR** (x.1.x): New features, new agents, new memory files
  - Example: Adding a new agent (e.g., security-agent.md)
  - Example: Adding a new language guide
  - Example: New memory file for a domain

- **PATCH** (x.x.1): Bug fixes, documentation improvements, small tweaks
  - Example: Fixing typos in agents or memory files
  - Example: Improving README clarity
  - Example: Fixing broken wiki links

## Staying Updated

If you used "Use this template" to create your own Vaulty repository, you can pull in framework updates:

```bash
# Add the template as a remote (one time setup)
git remote add template https://github.com/rhwendt/vaulty.git

# Check what version the template is on
git fetch template
git show template/main:CHANGELOG.md

# Merge updates from a specific version
git merge template/main --allow-unrelated-histories
```

**Tips for merging updates:**
- Framework files (agents/, memory/, templates/) can be merged
- Your personal files (config.md, projects/) won't conflict
- Review changes in CHANGELOG before merging
- Test with Claude after merging to ensure everything works

## Watching for Updates

Want to know when new versions are released?

1. **Watch Releases**: Click "Watch" → "Custom" → "Releases" on GitHub
2. **Star the repo**: Get notified of major updates
3. **Check periodically**: View this CHANGELOG or GitHub releases page

---

[Unreleased]: https://github.com/rhwendt/vaulty/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/rhwendt/vaulty/releases/tag/v1.0.0
