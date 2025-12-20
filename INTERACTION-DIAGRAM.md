# Vaulty Interaction Diagrams

Visual representations of how Vaulty's context memory system works.

## 1. Overall System Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                            USER                                  │
│              "Write a Python script to process CSV"              │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                          CLAUDE                                  │
│                                                                  │
│  Step 1: Read .claude.md (trigger words & workflows)            │
│          ↓                                                       │
│  Step 2: ⚠️  CHECK CONFIG FIRST! ⚠️                             │
│          Read config.md for user preferences:                    │
│          • languages: ["Python"]                                 │
│          • indent_size: 4                                        │
│          • repos_directory: "~/code"                             │
│          • test_framework_python: "pytest"                       │
│          ↓                                                       │
│  Step 3: Identify trigger → "Write" = Developer Agent workflow  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    AGENT WORKFLOW ACTIVATED                      │
│                                                                  │
│  Developer Agent → Tester Agent → Auditor Agent → Git Agent     │
└─────────────────────────────────────────────────────────────────┘
```

## 2. Detailed Agent Collaboration Workflow

```
USER REQUEST: "Write a Python function to validate emails"
│
▼
┌──────────────────────────────────────────────────────────────────┐
│ 1. DEVELOPER AGENT                                               │
│    ┌──────────────────────────────────────────────────────┐     │
│    │ • Read [[config]] → language: Python, indent: 4      │     │
│    │ • Read [[memory/architecture-design]] → patterns     │     │
│    │ • Read [[memory/code-review]] → standards            │     │
│    └──────────────────────────────────────────────────────┘     │
│    Writes code:                                                  │
│    ```python                                                     │
│    import re                                                     │
│    def validate_email(email: str) -> bool:                       │
│        pattern = r'^[\w\.-]+@[\w\.-]+\.\w+$'                     │
│        return bool(re.match(pattern, email))                     │
│    ```                                                           │
│    ✅ Code written → Send to Tester Agent                       │
└──────────────────────┬───────────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│ 2. TESTER AGENT                                                  │
│    ┌──────────────────────────────────────────────────────┐     │
│    │ • Read [[config]] → test_framework: pytest           │     │
│    │ • Read [[config]] → minimum_coverage: 70%            │     │
│    │ • Read [[memory/testing-qa]] → test standards        │     │
│    └──────────────────────────────────────────────────────┘     │
│    Writes tests:                                                 │
│    ```python                                                     │
│    def test_validate_email_valid():                              │
│        assert validate_email("user@example.com") == True         │
│    def test_validate_email_invalid():                            │
│        assert validate_email("invalid") == False                 │
│    ```                                                           │
│    Runs tests → ✅ All pass, 85% coverage                       │
│    ✅ Tests pass → Send to Auditor Agent                        │
└──────────────────────┬───────────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│ 3. AUDITOR AGENT                                                 │
│    ┌──────────────────────────────────────────────────────┐     │
│    │ • Read [[config]] → max_line_length: 100             │     │
│    │ • Read [[config]] → linter: pylint                   │     │
│    │ • Read [[memory/code-review]] → security checklist   │     │
│    └──────────────────────────────────────────────────────┘     │
│    Reviews code:                                                 │
│    ✓ No security issues (input validation present)              │
│    ✓ Code quality good (clear naming, proper types)             │
│    ✓ Tests adequate (>70% coverage requirement met)             │
│    ✓ Follows user's code style (4-space indent ✓)               │
│                                                                  │
│    ✅ AUDIT PASSED → Send to Git Agent                          │
└──────────────────────┬───────────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│ 4. GIT AGENT                                                     │
│    ┌──────────────────────────────────────────────────────┐     │
│    │ • Read [[config]] → repos_directory: "~/code"        │     │
│    │ • Read [[config]] → default_branch: "main"           │     │
│    │ • Read [[config]] → commit_style: "conventional"     │     │
│    │ • Read [[memory/git-workflow]] → best practices      │     │
│    └──────────────────────────────────────────────────────┘     │
│    Git operations:                                               │
│    $ cd ~/code/project                                           │
│    $ git status                                                  │
│    $ git add validate_email.py test_validate_email.py           │
│    $ git commit -m "feat: Add email validation function"        │
│    $ git push -u origin feature/email-validation                │
│                                                                  │
│    ✅ COMMITTED & PUSHED                                        │
└──────────────────────┬───────────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│                        RESULT TO USER                            │
│                                                                  │
│  ✅ Feature implemented with tests                              │
│  ✅ Code reviewed and approved                                  │
│  ✅ Committed to git                                            │
│                                                                  │
│  Files:                                                          │
│  - validate_email.py (implementation)                            │
│  - test_validate_email.py (tests with 85% coverage)             │
│  - Committed to: feature/email-validation                        │
└──────────────────────────────────────────────────────────────────┘
```

## 3. Configuration Flow Through System

```
┌─────────────────────────────────────────────────────────────────┐
│                        config.md                                 │
│                  (User's Personal Settings)                      │
│                                                                  │
│  name: "John Developer"                                          │
│  repos_directory: "~/code"                                       │
│  default_branch: "main"                                          │
│  languages: ["Python", "JavaScript"]                             │
│  indent_size: 4                                                  │
│  test_framework_python: "pytest"                                 │
│  minimum_coverage: 80                                            │
│  cloud_provider: "AWS"                                           │
│  ...                                                             │
└────────────┬───────────────────────────────┬────────────────────┘
             │                               │
    ┌────────┴────────┐           ┌──────────┴──────────┐
    │                 │           │                     │
    ▼                 ▼           ▼                     ▼
┌─────────┐    ┌──────────┐  ┌─────────┐        ┌──────────┐
│ Memory  │    │  Agents  │  │  .claude│        │  Claude  │
│  Files  │    │          │  │   .md   │        │          │
└─────────┘    └──────────┘  └─────────┘        └──────────┘
    │               │             │                   │
    │ References    │ References  │ References        │ Reads
    │ config for    │ config for  │ config as         │ config
    │ standards     │ behavior    │ priority #1       │ first
    │               │             │                   │
    ▼               ▼             ▼                   ▼
git-workflow    git-agent     "CHECK CONFIG       Uses user's
references:     checks:       FIRST!"             preferences
• default_      • repos_                          throughout
  branch          directory                       interaction
• commit_       • default_
  style           branch
• git_          • commit_
  workflow        style
```

## 4. Memory Files & Agent Relationships

```
┌─────────────────────────────────────────────────────────────────┐
│                        MEMORY FILES                              │
│                     (Best Practices)                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  git-workflow.md          documentation.md                       │
│  project-management.md    code-review.md                         │
│  testing-qa.md           communication.md                        │
│  deployment.md           architecture-design.md                  │
│                                                                  │
│  Each references [[config]] for user preferences                 │
└─────────────────────────────────────────────────────────────────┘
                             │
                             │ Referenced by
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     SPECIALIZED AGENTS                           │
│                  (Each checks config first!)                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │ Git Agent    │  │ Developer    │  │ Tester       │          │
│  │              │  │ Agent        │  │ Agent        │          │
│  │ Refs:        │  │              │  │              │          │
│  │ • git-       │  │ Refs:        │  │ Refs:        │          │
│  │   workflow   │  │ • arch-      │  │ • testing-   │          │
│  │              │  │   design     │  │   qa         │          │
│  └──────────────┘  │ • code-      │  └──────────────┘          │
│                    │   review     │                             │
│  ┌──────────────┐  └──────────────┘  ┌──────────────┐          │
│  │ Auditor      │                    │ Deployment   │          │
│  │ Agent        │  ┌──────────────┐  │ Agent        │          │
│  │              │  │ Project Mgr  │  │              │          │
│  │ Refs:        │  │ Agent        │  │ Refs:        │          │
│  │ • code-      │  │              │  │ • deployment │          │
│  │   review     │  │ Refs:        │  │              │          │
│  │ • testing-   │  │ • project-   │  └──────────────┘          │
│  │   qa         │  │   mgmt       │                             │
│  └──────────────┘  └──────────────┘  And 4 more agents...      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## 5. Quick Example Interactions

### Example A: "Commit these changes"

```
User: "Commit these changes"
  ↓
Claude reads .claude.md
  ↓ Trigger: "commit" → Git Agent
  ↓
Git Agent:
  1. Read config.md → repos_directory, default_branch, commit_style
  2. Read memory/git-workflow.md → best practices
  3. Run: git status, git diff
  4. Create commit message (conventional style from config)
  5. Commit and push
  ↓
Done! ✅
```

### Example B: "Deploy to production"

```
User: "Deploy to production"
  ↓
Claude reads .claude.md
  ↓ Trigger: "deploy" → Deployment Agent
  ↓
Deployment Agent:
  1. Read config.md → cloud_provider: AWS, environments
  2. Read memory/deployment.md → deployment checklist
  3. Run pre-deployment checks
  4. Deploy to AWS using user's CI/CD pipeline
  5. Monitor deployment
  ↓
Done! ✅
```

### Example C: "Fix this bug"

```
User: "The login function isn't working"
  ↓
Claude reads .claude.md
  ↓ Trigger: "bug" → Debugger Agent
  ↓
Debugger Agent:
  1. Read config.md → workspace, languages
  2. Investigate in user's workspace directory
  3. Find root cause
  ↓ Hands off to Developer Agent
Developer Agent:
  1. Read config.md → code style preferences
  2. Fix the bug
  ↓ Hands off to Tester Agent
Tester Agent:
  1. Read config.md → test framework
  2. Add regression test
  ↓ Hands off to Auditor Agent
Auditor Agent:
  1. Review fix
  ↓ If PASS, hand off to Git Agent
Git Agent:
  1. Read config.md → git preferences
  2. Commit the fix
  ↓
Done! ✅
```

## Key Takeaways

1. **Config First**: Every operation starts by checking user's config
2. **Memory Files**: Provide best practices for each domain
3. **Agents**: Specialized experts that reference config + memory
4. **Workflows**: Agents collaborate (Developer → Tester → Auditor → Git)
5. **Personalized**: Everything adapts to YOUR tools, style, and workflow

---

*This is a living document. As you use Vaulty, you'll see these patterns in action!*
