#config #user-settings

# User Configuration

> [!IMPORTANT]
> This is your personal configuration file. Copy this template to `config.md` and customize it with your settings. The `config.md` file is git-ignored, so your personal settings won't be committed.

## Setup Instructions

1. Copy this file: `cp config.template.md config.md`
2. Edit `config.md` with your personal settings
3. Agents and memory files will reference your config automatically

---

## Personal Information

### Identity
```yaml
name: "Your Full Name"
email: "your.email@example.com"
username: "yourusername"
pronouns: "they/them"  # Optional
```

### Contact
```yaml
github: "https://github.com/yourusername"
linkedin: "https://linkedin.com/in/yourprofile"  # Optional
website: "https://yourwebsite.com"  # Optional
```

---

## Local Environment

### Directory Structure
```yaml
# Where you store your code projects
repos_directory: "~/code"

# Alternative repo locations
personal_projects: "~/projects/personal"
work_projects: "~/projects/work"
learning_projects: "~/learning"

# Workspace directory for active work
workspace: "~/workspace"

# Where you store documentation
docs_directory: "~/Documents/dev-docs"
```

### Development Tools
```yaml
# Primary code editor/IDE
editor: "VS Code"  # Options: VS Code, Vim, Neovim, Emacs, IntelliJ, etc.
editor_command: "code"  # Command to open editor from terminal

# Terminal
terminal: "iTerm2"  # Options: iTerm2, Terminal, Alacritty, etc.

# Shell
shell: "zsh"  # Options: zsh, bash, fish, etc.

# Package managers by language/platform
package_manager_python: "pip"  # Options: pip, poetry, pipenv, conda
package_manager_node: "npm"  # Options: npm, yarn, pnpm, bun
package_manager_go: "go mod"  # Standard: go mod (built-in)
package_manager_rust: "cargo"  # Standard: cargo (built-in)
package_manager_java: "Maven"  # Options: Maven, Gradle
package_manager_csharp: "NuGet"  # Standard: NuGet (.NET CLI)
package_manager_cpp: "Conan"  # Options: Conan, vcpkg, CMake
package_manager_php: "Composer"  # Standard: Composer
package_manager_ruby: "Bundler"  # Standard: Bundler (gem)
package_manager_swift: "Swift Package Manager"  # Options: Swift Package Manager, CocoaPods, Carthage
package_manager_kotlin: "Gradle"  # Options: Gradle, Maven
package_manager_system: "brew"  # Options: brew, apt, yum, pacman, etc.
```

---

## Git Configuration

### Git Preferences
```yaml
# Default branch name for new repos
default_branch: "main"  # Options: main, master, develop

# Commit message style
commit_style: "conventional"  # Options: conventional, semantic, custom
# conventional = "feat:", "fix:", "docs:", etc.
# semantic = similar to conventional
# custom = your own style

# Git workflow
git_workflow: "feature-branch"  # Options: feature-branch, gitflow, trunk-based

# Always sign commits
sign_commits: false  # true/false

# GPG key (if signing commits)
gpg_key: ""  # Your GPG key ID
```

### Remote Preferences
```yaml
# Preferred git host
git_host: "GitHub"  # Options: GitHub, GitLab, Bitbucket, etc.

# Default remote name
default_remote: "origin"

# SSH vs HTTPS
git_protocol: "SSH"  # Options: SSH, HTTPS
```

---

## Project Preferences

### Language & Framework Preferences
```yaml
# Primary programming languages (in order of preference)
languages:
  - "Python"
  - "JavaScript"
  - "TypeScript"
  - "Go"
  - "Rust"
  - "Java"
  - "C#"
  - "C++"
  - "PHP"
  - "Ruby"
  - "Swift"
  - "Kotlin"

# Preferred frameworks by language
frameworks:
  python: "Flask"  # Options: Flask, Django, FastAPI, Pyramid
  javascript: "React"  # Options: React, Vue, Angular, Svelte, Next.js
  typescript: "Next.js"  # Options: Next.js, Nest.js, Angular, Vue
  go: "Gin"  # Options: Gin, Echo, Fiber, Chi, Gorilla Mux
  rust: "Actix Web"  # Options: Actix Web, Rocket, Axum, Warp
  java: "Spring Boot"  # Options: Spring Boot, Quarkus, Micronaut, Jakarta EE
  csharp: "ASP.NET Core"  # Options: ASP.NET Core, .NET MAUI
  cpp: "Qt"  # Options: Qt, Boost, POCO
  php: "Laravel"  # Options: Laravel, Symfony, CodeIgniter, Slim
  ruby: "Rails"  # Options: Rails, Sinatra, Hanami
  swift: "SwiftUI"  # Options: SwiftUI, UIKit, Vapor (backend)
  kotlin: "Spring Boot"  # Options: Spring Boot, Ktor, Android (Jetpack Compose)
  backend: "Node.js"  # Options: Node.js, Flask, Django, Express, Go, Rust

# Database preferences
database_relational: "PostgreSQL"  # Options: PostgreSQL, MySQL, SQLite, MariaDB, Oracle
database_nosql: "MongoDB"  # Options: MongoDB, Redis, DynamoDB, Cassandra, CouchDB
database_cache: "Redis"  # Options: Redis, Memcached
database_graph: "Neo4j"  # Options: Neo4j, ArangoDB, JanusGraph
database_timeseries: "InfluxDB"  # Options: InfluxDB, TimescaleDB, Prometheus
```

### Code Style
```yaml
# Indentation
indent_style: "spaces"  # Options: spaces, tabs
indent_size: 4  # Number of spaces (2 or 4 common)

# Line length
max_line_length: 100  # Typically 80, 100, or 120

# Code formatters by language
formatter_python: "black"  # Options: black, autopep8, yapf, ruff
formatter_javascript: "prettier"  # Options: prettier, eslint
formatter_typescript: "prettier"  # Options: prettier, eslint
formatter_go: "gofmt"  # Options: gofmt, goimports
formatter_rust: "rustfmt"  # Options: rustfmt
formatter_java: "google-java-format"  # Options: google-java-format, Eclipse formatter
formatter_csharp: "dotnet format"  # Options: dotnet format, csharpier
formatter_cpp: "clang-format"  # Options: clang-format
formatter_php: "PHP-CS-Fixer"  # Options: PHP-CS-Fixer, phpfmt
formatter_ruby: "RuboCop"  # Options: RuboCop, Prettier for Ruby
formatter_swift: "SwiftFormat"  # Options: SwiftFormat, swift-format
formatter_kotlin: "ktlint"  # Options: ktlint, kotlinter

# Linters by language
linter_python: "pylint"  # Options: pylint, flake8, ruff, mypy (type checker)
linter_javascript: "eslint"  # Options: eslint, standard
linter_typescript: "eslint"  # Options: eslint, tslint (deprecated)
linter_go: "golangci-lint"  # Options: golangci-lint, staticcheck, go vet
linter_rust: "clippy"  # Options: clippy
linter_java: "checkstyle"  # Options: checkstyle, PMD, SpotBugs
linter_csharp: "Roslyn analyzers"  # Options: Roslyn analyzers, StyleCop
linter_cpp: "clang-tidy"  # Options: clang-tidy, cppcheck
linter_php: "PHPStan"  # Options: PHPStan, Psalm, PHP_CodeSniffer
linter_ruby: "RuboCop"  # Options: RuboCop, Reek
linter_swift: "SwiftLint"  # Options: SwiftLint
linter_kotlin: "ktlint"  # Options: ktlint, detekt
```

---

## Testing & QA

### Testing Preferences
```yaml
# Test framework preferences by language
test_framework_python: "pytest"  # Options: pytest, unittest, nose2, doctest
test_framework_javascript: "jest"  # Options: jest, mocha, vitest, jasmine
test_framework_typescript: "jest"  # Options: jest, vitest, mocha with ts-node
test_framework_go: "testing"  # Options: testing (built-in), testify, ginkgo
test_framework_rust: "cargo test"  # Options: cargo test (built-in), rstest
test_framework_java: "JUnit 5"  # Options: JUnit 5, TestNG, Spock
test_framework_csharp: "xUnit"  # Options: xUnit, NUnit, MSTest
test_framework_cpp: "Google Test"  # Options: Google Test, Catch2, Boost.Test
test_framework_php: "PHPUnit"  # Options: PHPUnit, Pest, Codeception
test_framework_ruby: "RSpec"  # Options: RSpec, Minitest, Test::Unit
test_framework_swift: "XCTest"  # Options: XCTest, Quick & Nimble
test_framework_kotlin: "JUnit 5"  # Options: JUnit 5, Kotest, Spek

# Coverage requirements
minimum_coverage: 70  # Percentage (e.g., 70, 80, 90)
critical_path_coverage: 90  # Coverage for critical code paths

# Test file naming patterns by language
test_file_pattern_python: "test_*.py"  # Options: test_*.py, *_test.py
test_file_pattern_javascript: "*.test.js"  # Options: *.test.js, *.spec.js
test_file_pattern_typescript: "*.test.ts"  # Options: *.test.ts, *.spec.ts
test_file_pattern_go: "*_test.go"  # Standard: *_test.go
test_file_pattern_rust: "tests/"  # Standard: tests/ directory or #[cfg(test)] modules
test_file_pattern_java: "*Test.java"  # Options: *Test.java, *Tests.java
test_file_pattern_csharp: "*Tests.cs"  # Options: *Tests.cs, *Test.cs
test_file_pattern_cpp: "*_test.cpp"  # Options: *_test.cpp, *Test.cpp
test_file_pattern_php: "*Test.php"  # Standard: *Test.php
test_file_pattern_ruby: "*_spec.rb"  # Standard: *_spec.rb (RSpec)
test_file_pattern_swift: "*Tests.swift"  # Standard: *Tests.swift
test_file_pattern_kotlin: "*Test.kt"  # Standard: *Test.kt
```

---

## Deployment & Infrastructure

### Deployment Preferences
```yaml
# Primary cloud provider
cloud_provider: "AWS"  # Options: AWS, GCP, Azure, DigitalOcean, Heroku

# Container platform
container_platform: "Docker"  # Options: Docker, Podman

# Orchestration (if using)
orchestration: "Kubernetes"  # Options: Kubernetes, Docker Compose, none

# CI/CD platform
cicd_platform: "GitHub Actions"  # Options: GitHub Actions, GitLab CI, CircleCI, Jenkins

# Preferred deployment strategy
deployment_strategy: "blue-green"  # Options: blue-green, canary, rolling
```

### Environment Names
```yaml
environments:
  - "development"
  - "staging"
  - "production"

# Environment-specific URLs (optional)
environment_urls:
  development: "http://localhost:3000"
  staging: "https://staging.yourapp.com"
  production: "https://yourapp.com"
```

---

## Team & Organization

### Team Settings
```yaml
# Team name
team_name: "Development Team"

# Organization
organization: "Your Company"

# Team size
team_size: "small"  # Options: solo, small (2-5), medium (6-15), large (15+)

# Communication channels
slack_workspace: ""  # Your Slack workspace
discord_server: ""  # Your Discord server
```

### Workflow Preferences
```yaml
# Sprint length (for agile teams)
sprint_length: "2 weeks"  # Options: 1 week, 2 weeks, 3 weeks

# Daily standup time
standup_time: "9:30 AM"  # Optional

# Code review requirements
code_review_required: true
minimum_reviewers: 1  # Number of required reviewers

# Meeting preferences
meeting_preference: "async-first"  # Options: async-first, sync-required, hybrid
```

---

## Documentation Preferences

### Documentation Style
```yaml
# Documentation format
doc_format: "Markdown"  # Options: Markdown, reStructuredText, AsciiDoc

# Docstring style
docstring_style: "Google"  # Options: Google, NumPy, Sphinx

# README template preference
readme_style: "detailed"  # Options: minimal, standard, detailed

# ADR (Architecture Decision Records)
use_adrs: true  # true/false
adr_directory: "docs/decisions"
```

---

## Security & Compliance

### Security Preferences
```yaml
# Security scanning
security_scanner: "Snyk"  # Options: Snyk, GitHub Dependabot, npm audit

# Secret management
secret_manager: "environment variables"  # Options: AWS Secrets Manager, Vault, env vars

# License preference for new projects
preferred_license: "MIT"  # Options: MIT, Apache-2.0, GPL-3.0, Proprietary
```

---

## Custom Preferences

### Custom Shortcuts
```yaml
# Your custom trigger words for common tasks
custom_triggers:
  quick_commit: "qc"  # e.g., "qc this change" = quick commit
  deploy_staging: "ds"  # e.g., "ds" = deploy to staging
  run_tests: "rt"  # e.g., "rt" = run test suite
```

### Project Templates
```yaml
# Your frequently used project structures
project_templates:
  - "python-flask-api"
  - "react-frontend"
  - "full-stack-web-app"
  - "data-science-notebook"
```

### Reminders
```yaml
# Things you want Claude to always remind you about
reminders:
  - "Always update CHANGELOG.md before releasing"
  - "Run security scan before deploying to production"
  - "Update API documentation when endpoints change"
```

---

## Working Hours & Availability

### Schedule (Optional)
```yaml
# Your typical working hours
working_hours:
  start: "9:00 AM"
  end: "5:00 PM"
  timezone: "EST"  # Your timezone

# Preferred days for deployments
deployment_days:
  - "Tuesday"
  - "Wednesday"
  - "Thursday"
# Note: Avoid Friday deployments unless critical

# Focus time (no meetings)
focus_time: "9:00 AM - 11:00 AM"
```

---

## Notes

### Additional Context
```yaml
# Any additional information that might be useful for Claude
notes: |
  - I prefer detailed explanations for complex topics
  - When in doubt, ask clarifying questions
  - I value code readability over cleverness
  - Performance matters, but not at the cost of maintainability
```

---

## Quick Reference

**Config File Location**: `config.md` (git-ignored, your personal copy)
**Template Location**: `config.template.md` (committed to repo)

**How Agents Use This**:
- Git Agent → References git preferences, repo directories
- Developer Agent → References language preferences, code style
- Tester Agent → References testing frameworks, coverage requirements
- Deployment Agent → References cloud provider, deployment strategy
- All Agents → Reference your personal preferences and workflow

**Updating Your Config**:
1. Edit `config.md` anytime
2. Changes take effect immediately
3. No need to commit (it's git-ignored)
4. Update `config.template.md` if you want to share template improvements

---

**Last Updated**: [Date you last updated your config]
