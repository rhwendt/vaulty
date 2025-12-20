#memory/code-review #workflow

# Code Review Best Practices

## Code Review Philosophy

- **Collaborative, not combative** - We're all on the same team
- **Objective feedback** - Focus on code, not the person
- **Learning opportunity** - Both reviewer and author learn
- **Quality gate** - Catch issues before they reach production

## For Code Authors

### Before Requesting Review

**Self-Review Checklist**
- [ ] Code works and meets requirements
- [ ] Tests pass locally
- [ ] Code follows project style/conventions
- [ ] No debugging code or commented-out sections
- [ ] Documentation updated if needed
- [ ] Commit messages are clear
- [ ] PR description is complete

### Creating a Good PR

**PR Title**
```
type: Brief description of changes

Examples:
feat: Add user authentication
fix: Resolve memory leak in data processing
refactor: Simplify validation logic
```

**PR Description Template**
```markdown
## Summary
Brief overview of what this PR does (2-3 sentences).

## Changes
- Bullet point list of main changes
- Organized by type or component
- Include why, not just what

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Edge cases covered

## Screenshots (if applicable)
[Before/after screenshots for UI changes]

## Related
- Fixes #issue-number
- Related to [[task-file]]
- Depends on #pr-number

## Notes for Reviewers
- Specific areas where you want feedback
- Known limitations or tradeoffs
- Future work planned
```

### Responding to Feedback

- **Be receptive** - Reviews help improve your code
- **Ask questions** - If feedback is unclear, ask for clarification
- **Explain decisions** - If you disagree, explain your reasoning
- **Make changes promptly** - Address feedback quickly
- **Mark resolved** - Indicate when you've addressed comments

## For Code Reviewers

### Review Approach

**Review Within**
- Critical PRs: Same day
- Normal PRs: 24-48 hours
- Small fixes: Few hours

**Review Focus Areas**
1. **Correctness**: Does it work? Does it meet requirements?
2. **Design**: Is it well-structured? Does it fit the architecture?
3. **Readability**: Is it clear? Can others understand it?
4. **Testing**: Are there adequate tests?
5. **Security**: Any vulnerabilities or risks?
6. **Performance**: Any efficiency concerns?

### Review Checklist

**Functionality**
- [ ] Code does what it's supposed to do
- [ ] Edge cases are handled
- [ ] Error handling is appropriate
- [ ] No obvious bugs

**Code Quality**
- [ ] Code is readable and maintainable
- [ ] Names are clear and descriptive
- [ ] Functions/methods are focused and small
- [ ] No unnecessary complexity
- [ ] Follows DRY principle
- [ ] Consistent with codebase style

**Security**
- [ ] No SQL injection vulnerabilities
- [ ] No XSS vulnerabilities
- [ ] Input validation is proper
- [ ] Sensitive data is protected
- [ ] Authentication/authorization is correct
- [ ] No hardcoded secrets or credentials

**Testing**
- [ ] Tests cover main functionality
- [ ] Tests cover edge cases
- [ ] Tests are clear and maintainable
- [ ] Mocks are used appropriately

**Documentation**
- [ ] Complex logic is commented
- [ ] Public APIs are documented
- [ ] README updated if needed
- [ ] Breaking changes are noted

**Performance**
- [ ] No obvious performance issues
- [ ] Database queries are efficient
- [ ] No N+1 query problems
- [ ] Caching used where appropriate

### Providing Feedback

**Comment Types**

Use prefixes to clarify intent:
- **[nit]**: Minor suggestion, not blocking
- **[question]**: Asking for clarification
- **[suggestion]**: Alternative approach to consider
- **[concern]**: Issue that should be addressed
- **[blocking]**: Must be fixed before merge

**Good Feedback Examples**

✅ **Constructive**
```
[suggestion] Consider extracting this logic into a separate function
for better testability and reuse. What do you think about creating
a `validateUserInput()` helper?
```

✅ **Specific**
```
[concern] This query could cause N+1 problem. Since we're iterating
over users and fetching posts for each, consider using a JOIN or
eager loading (line 42).
```

✅ **Educational**
```
[nit] We typically use `const` instead of `let` for values that
don't change. This helps prevent accidental reassignment.
```

❌ **Avoid**
```
This is wrong.
Why did you do it this way?
This code is terrible.
```

### Approval Guidelines

**Approve When**
- Code meets quality standards
- All blocking issues are addressed
- Tests pass and coverage is adequate
- Documentation is sufficient

**Request Changes When**
- Critical bugs exist
- Security vulnerabilities present
- Tests are missing or insufficient
- Major design issues

**Comment (No Approval) When**
- You have questions but aren't blocking
- You want to provide optional suggestions
- You're not the primary reviewer

## Review Workflow

### 1. Initial Review
```
1. Read PR description and understand context
2. Check that tests pass in CI
3. Review code changes file by file
4. Leave comments and questions
5. Test locally if needed
6. Provide summary and decision (approve/request changes/comment)
```

### 2. Follow-up Review
```
1. Check that previous feedback was addressed
2. Review new changes
3. Approve if satisfied, or continue discussion
```

### 3. Merge
```
1. Ensure all approvals are in place
2. Ensure CI passes
3. Merge using appropriate strategy (squash, merge, rebase)
4. Delete feature branch after merge
```

## Common Review Patterns

### Security Red Flags
- User input directly in SQL queries
- Eval or exec of dynamic code
- Missing authentication checks
- Exposed sensitive data in logs
- Weak password requirements

### Performance Red Flags
- N+1 database queries
- Missing indexes on queried columns
- Loading entire datasets in memory
- Inefficient algorithms (O(n²) when O(n) possible)
- Missing pagination on large datasets

### Maintainability Red Flags
- Functions > 50 lines
- Deeply nested conditionals (> 3 levels)
- Magic numbers without constants
- Duplicated code
- Poor naming (x, temp, data)

## Tips for Effective Reviews

**For Authors**
- Keep PRs small (< 400 lines when possible)
- One logical change per PR
- Provide context in description
- Be responsive to feedback

**For Reviewers**
- Review promptly
- Be kind and constructive
- Focus on important issues
- Approve good-enough code (don't be a perfectionist)
- Teach, don't just critique

## Related

- [[git-workflow]] - For branch and commit practices
- [[testing-qa]] - For testing standards
- [[communication]] - For team communication
