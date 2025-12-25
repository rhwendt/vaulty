# Changelog

All notable changes to the Vaulty framework will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [2.0.0] - 2025-01-XX

### ⚠️ BREAKING CHANGES

This is a major restructuring to align with official Claude Code best practices. **Migration required** for existing users.

#### Directory Restructuring
- **BREAKING**: Renamed `.claude.md` → `CLAUDE.md` (uppercase, no dot prefix)
- **BREAKING**: Moved `memory/` → `.claude/rules/` (official auto-loaded rules location)
- **BREAKING**: Moved `agents/` → `.claude/agents/` (official subagents location)

#### Agent System Overhaul
- **BREAKING**: Converted all agents from custom format to official Claude Code subagent format
- **BREAKING**: All agents now use YAML frontmatter (name, description, tools)
- **BREAKING**: Changed from manual invocation to automatic delegation based on descriptions
- Removed extensive agent documentation sections (now concise prompts)
- Agents no longer hardcode model selection - users control models via environment variables

#### File Size Reduction
- Reduced codebase by **4,019 lines** (4,850 deletions, 831 insertions)
- Simplified agent files by 70-90% each
- More concise CLAUDE.md (now focuses on system overview)

### Added
- Official YAML frontmatter format for all subagents
- Automatic delegation system (no manual invocation needed)
- Separate context windows for each subagent
- Environment Configuration section in README.md with model control guidance
- Comprehensive environment variable documentation (CLAUDE_CODE_SUBAGENT_MODEL, CLAUDE_ENV_FILE, etc.)
- Shell configuration examples for zsh/bash
- AWS Bedrock and Google Vertex AI setup instructions
- Updated README "Staying Updated" section with new paths
- Migration guidance in CHANGELOG

### Changed
- CLAUDE.md now emphasizes automatic delegation
- All wiki-links updated: `[[memory/...]]` → `[[rules/...]]`
- Subagent names use kebab-case (e.g., `git-helper`, `software-designer`)
- Agent collaboration workflows now describe automatic coordination
- Model selection now user-controlled via environment variables (no hardcoded models)
- Agents default to Sonnet; users can override via CLAUDE_CODE_SUBAGENT_MODEL

### Migration Guide
For users with existing Vaulty repos:
1. **Save your customizations**: `config.md`, `projects/*`, any custom rules
2. **Fetch template updates**: `git fetch template`
3. **Merge or cherry-pick**: Choose merge strategy based on your customizations
4. **Resolve conflicts**: Keep your `config.md` and `projects/`, accept template changes for framework files
5. See README "Staying Updated" section for detailed instructions

## [1.1.0] - 2025-01-XX

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
- **New agent**: Project Designer Agent for designing new projects from scratch
  - Planning-first approach with requirements gathering
  - Technology exploration and trade-off analysis
  - Architecture design and implementation roadmaps
  - Hands off to Architect, Project Manager, and Developer agents
  - Includes comprehensive workflow documentation
  - Updated README with new agent and workflow examples
  - Updated INTERACTION-DIAGRAM.md with project design workflow diagram
  - Updated .claude.md with trigger patterns and collaboration workflows

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

- **MAJOR** (2.x.x): Breaking changes to core framework structure
  - Example: Restructuring directory layout (v2.0: memory/ → .claude/rules/)
  - Example: Changing agent format (v2.0: custom → official subagents)
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
- Framework files (`.claude/rules/`, `.claude/agents/`, templates/) can be merged
- Your personal files (`config.md`, `projects/`) won't conflict
- Review changes in CHANGELOG before merging (especially for BREAKING CHANGES)
- Test with Claude after merging to ensure everything works
- For v2.0+ migration, see "Migration Guide" in v2.0.0 changelog entry

## Watching for Updates

Want to know when new versions are released?

1. **Watch Releases**: Click "Watch" → "Custom" → "Releases" on GitHub
2. **Star the repo**: Get notified of major updates
3. **Check periodically**: View this CHANGELOG or GitHub releases page

---

[Unreleased]: https://github.com/rhwendt/vaulty/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/rhwendt/vaulty/compare/v1.1.0...v2.0.0
[1.1.0]: https://github.com/rhwendt/vaulty/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/rhwendt/vaulty/releases/tag/v1.0.0
