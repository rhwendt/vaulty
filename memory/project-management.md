#memory/project-management #workflow

# Project Management Best Practices

> [!NOTE] User Configuration
> Check [[config]] for your personal project management preferences:
> - `team_name`: Your team name
> - `organization`: Your organization
> - `team_size`: Team size (solo, small, medium, large)
> - `sprint_length`: Sprint/iteration length
> - `code_review_required`: Whether code review is required
> - `minimum_reviewers`: Number of required reviewers

## Project Structure

Each project should have:
- **Overview file**: High-level description, goals, and status
- **Tasks directory**: Individual task files with structured tracking
- **Documentation**: Project-specific docs and decisions
- **Resources**: Links, references, and assets

## Task Management

### Task File Structure

Each task is a separate markdown file with:

```markdown
#task #status/[pending|in-progress|completed|blocked]

# Task: [Short Title]

**Project**: [[project-name]]
**Priority**: [High|Medium|Low]
**Status**: [Pending|In Progress|Completed|Blocked]
**Created**: YYYY-MM-DD
**Updated**: YYYY-MM-DD
**Due Date**: YYYY-MM-DD (optional)

## Description

Clear description of what needs to be done.

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Progress Notes

### YYYY-MM-DD
- Note about progress
- Decisions made
- Blockers encountered

## Related

- [[related-task]]
- [[project-overview]]
```

### Task Naming Convention

`task-NNN-short-description.md`

Examples:
- `task-001-setup-database.md`
- `task-002-implement-auth.md`
- `task-003-fix-login-bug.md`

### Task Statuses

Use Obsidian tags for easy filtering:
- `#status/pending` - Not started
- `#status/in-progress` - Currently working on
- `#status/completed` - Finished
- `#status/blocked` - Cannot proceed (note blocker in description)

## Project Workflow

### 1. Project Initialization
- Create project directory under `projects/`
- Copy and customize overview template
- Create initial tasks
- Link to relevant memory files

### 2. Active Development
- Work on one task at a time
- Update task status regularly
- Document decisions and progress
- Create new tasks as needed

### 3. Task Completion
- Verify acceptance criteria met
- Update task status to completed
- Update project overview
- Archive or organize completed tasks

### 4. Project Reviews
- Weekly: Review active tasks and priorities
- Monthly: Review project progress and goals
- Update project overview with status

## Priority Guidelines

**High Priority**
- Blockers for other work
- Critical bugs
- Time-sensitive deliverables

**Medium Priority**
- Important features
- Technical debt
- Non-critical bugs

**Low Priority**
- Nice-to-have features
- Optimizations
- Documentation improvements

## Estimation Approach

Use t-shirt sizing for tasks:
- **XS**: < 2 hours
- **S**: 2-4 hours
- **M**: 1-2 days
- **L**: 3-5 days
- **XL**: 1+ weeks (break down further)

## Communication

- Document decisions in task files
- Use project overview for status updates
- Link related tasks and projects
- Keep stakeholders informed of blockers

## Tips for Success

- Break large tasks into smaller ones
- Update task status promptly
- Document why, not just what
- Review and prioritize regularly
- Keep tasks focused and atomic

## Related

- [[git-workflow]] - For committing and versioning work
- [[documentation]] - For documenting projects
- [[communication]] - For team communication standards
