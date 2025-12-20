#memory/git #workflow

# Git Workflow Best Practices

> [!NOTE] User Configuration
> Check [[config]] for your personal git preferences:
> - `default_branch`: Your preferred default branch name
> - `repos_directory`: Where you store your repositories
> - `git_workflow`: Your preferred workflow (feature-branch, gitflow, etc.)
> - `commit_style`: Your commit message style preference
> - `sign_commits`: Whether you sign commits
> - `git_protocol`: SSH vs HTTPS preference

## Branch Strategy

- **Main Branch**: `{config.default_branch}` (check your [[config]] - typically `main` or `master`) - production-ready code only
- **Feature Branches**: `feature/descriptive-name` or `claude/task-name-{sessionId}`
- **Bug Fixes**: `fix/issue-description`
- **Hotfixes**: `hotfix/critical-fix`

## Commit Guidelines

### Commit Message Format
```
<type>: <short summary>

<optional detailed description>

<optional footer with issue references>
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style/formatting (no logic change)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks, dependencies

### Best Practices
- Write clear, descriptive commit messages
- Keep commits atomic (one logical change per commit)
- Commit early and often
- Don't commit sensitive data, credentials, or secrets
- Review changes with `git diff` before committing

## Workflow Steps

### Starting New Work
```bash
git checkout main
git pull origin main
git checkout -b feature/descriptive-name
```

### During Development
```bash
git status                    # Check what's changed
git diff                      # Review changes
git add <files>              # Stage specific files
git commit -m "type: message" # Commit with clear message
```

### Before Pushing
```bash
git log --oneline -5         # Review recent commits
git diff main...HEAD         # See all changes vs main
```

### Pushing Changes
```bash
git push -u origin <branch-name>
```
**Retry Logic**: If push fails due to network errors, retry up to 4 times with exponential backoff (2s, 4s, 8s, 16s)

### Creating Pull Requests
- Provide clear title and description
- Reference related issues
- Include test plan
- Request reviews from relevant team members

## Pre-Commit Checks

- [ ] Run tests and ensure they pass
- [ ] Run linters/formatters
- [ ] Check for secrets or sensitive data
- [ ] Review diff for unintended changes
- [ ] Ensure commit messages are clear

## Common Commands

```bash
git status                    # Check working tree status
git log --oneline            # View commit history
git diff                     # See unstaged changes
git diff --staged            # See staged changes
git reset HEAD <file>        # Unstage a file
git checkout -- <file>       # Discard changes to a file
git stash                    # Temporarily save changes
git stash pop                # Restore stashed changes
```

## Things to Avoid

- ❌ Never force push to main/master
- ❌ Never skip hooks without good reason
- ❌ Never commit directly to main (use PRs)
- ❌ Never rewrite public history
- ❌ Never commit files with secrets (.env, credentials, keys)

## Related

- [[project-management]] - For tracking work in projects
- [[code-review]] - For PR review guidelines
