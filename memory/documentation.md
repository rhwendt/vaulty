#memory/documentation #workflow

# Documentation Best Practices

## Documentation Philosophy

- **Write for your future self** - You'll forget context
- **Write for others** - Someone else will read this
- **Keep it current** - Outdated docs are worse than no docs
- **Make it discoverable** - Good organization and linking

## Types of Documentation

### 1. Project Documentation
- **Overview/README**: What, why, how to get started
- **Architecture**: System design and structure
- **Setup Guide**: Installation and configuration
- **User Guide**: How to use the system
- **Contributing**: How to contribute to the project

### 2. Code Documentation
- **Inline Comments**: Explain complex logic or non-obvious decisions
- **Function/Method Docs**: Parameters, returns, behavior
- **API Documentation**: Endpoints, requests, responses
- **Examples**: Code snippets showing usage

### 3. Process Documentation
- **Workflows**: Step-by-step procedures
- **Decision Records**: Why choices were made
- **Runbooks**: Operational procedures
- **Troubleshooting**: Common issues and solutions

## Documentation Structure

### Project README Template

```markdown
# Project Name

Brief description of what this project does.

## Overview

Detailed description of the project, its purpose, and value.

## Getting Started

### Prerequisites
- Requirement 1
- Requirement 2

### Installation
```bash
step-by-step commands
```

### Quick Start
```bash
how to run/use
```

## Usage

Common usage examples with code snippets.

## Architecture

High-level overview of system design.

## Contributing

How to contribute (see git-workflow).

## License

License information.

## Contact

How to get help or contact maintainers.
```

### Decision Records

Use ADR (Architecture Decision Record) format:

```markdown
# ADR-NNN: [Decision Title]

**Status**: [Proposed|Accepted|Deprecated|Superseded]
**Date**: YYYY-MM-DD
**Deciders**: [Who was involved]

## Context

What is the issue we're facing?

## Decision

What did we decide to do?

## Consequences

What are the positive and negative outcomes?

## Alternatives Considered

What other options did we evaluate?
```

## Documentation Standards

### Markdown Best Practices
- Use clear headings (H1 for title, H2 for sections)
- Use code blocks with language syntax highlighting
- Use tables for structured data
- Use lists for sequential or grouped items
- Use links to connect related docs

### Obsidian-Specific
- Use `[[wiki-links]]` to link between docs
- Use tags for categorization (`#tag`)
- Use callouts for important notes
- Use dataview queries for dynamic content (optional)

### Code Documentation
```python
def function_name(param1: Type, param2: Type) -> ReturnType:
    """
    Brief description of what the function does.

    Args:
        param1: Description of param1
        param2: Description of param2

    Returns:
        Description of return value

    Raises:
        ExceptionType: When this exception occurs

    Example:
        >>> function_name(value1, value2)
        expected_output
    """
```

## What to Document

### ✅ Always Document
- Public APIs and interfaces
- Complex algorithms or business logic
- Non-obvious design decisions
- Setup and configuration steps
- Known issues and workarounds
- Breaking changes

### ⚠️ Document Sparingly
- Self-explanatory code
- Standard patterns
- Obvious functionality

### ❌ Don't Document
- What the code does (code should be self-documenting)
- Commented-out code (delete it, git remembers)
- Placeholder/TODO without context

## Documentation Workflow

### When Starting a Project
1. Create project overview
2. Document initial architecture decisions
3. Set up basic README
4. Create setup/installation guide

### During Development
1. Update docs when behavior changes
2. Document decisions in ADRs
3. Add code comments for complex logic
4. Keep README current

### Before Completion
1. Review all documentation for accuracy
2. Add usage examples
3. Document known issues
4. Create troubleshooting guide if needed

## Maintenance

- **Review Regularly**: Check docs during code reviews
- **Update Promptly**: Update docs with code changes
- **Archive Outdated**: Mark deprecated content clearly
- **Solicit Feedback**: Ask users if docs are helpful

## Tools and Formatting

### Obsidian Features
- **Links**: `[[other-file]]` or `[[other-file|display text]]`
- **Tags**: `#category/subcategory`
- **Callouts**:
  ```markdown
  > [!NOTE]
  > Important information here

  > [!WARNING]
  > Caution information here
  ```

### Diagrams
- Use Mermaid for flowcharts and diagrams
- Use ASCII art for simple diagrams
- Link to external diagram tools if needed

## Related

- [[project-management]] - For organizing project work
- [[communication]] - For team communication
- [[architecture-design]] - For technical design docs
