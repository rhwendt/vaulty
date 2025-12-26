# Vaulty

Context memory system for Claude Code.

## Critical

- **ALWAYS check config.md first** - Contains user preferences for tools, frameworks, code style, and commands

## Project Structure

- `config.md` - User preferences and commands (CHECK FIRST)
- `.claude/rules/` - Auto-loaded guidelines (code style, git, testing, deployment, languages)
- `.claude/agents/` - Specialized subagents (auto-delegated based on task)
- `projects/` - Structured project management with templates

## Notes

- This is an Obsidian vault - use `[[wiki-links]]` and tags
- Rules and agents are auto-loaded - no need to reference them manually
- User config overrides default behavior
