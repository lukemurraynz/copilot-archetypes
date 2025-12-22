---
applyTo: "**/*.md,**/docs/**"
description: 'Documentation and Markdown best practices following ISE Engineering Playbook guidelines'
---

# Documentation and Markdown Instructions


## When to Apply

This instruction applies when:
- Creating or modifying Markdown files
- Writing README files
- Creating technical documentation
- Writing decision records (ADRs)

## README Structure

A good README should include:

```markdown
# Project Name

Brief description of what the project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Prerequisites

- Requirement 1
- Requirement 2

## Installation

Step-by-step installation instructions.

## Usage

Basic usage examples with code snippets.

## Configuration

Configuration options and environment variables.

## Contributing

Link to CONTRIBUTING.md or brief contribution guidelines.

## License

License information.
```

## Markdown Formatting

### Headings

Use proper heading hierarchy:

```markdown
# Title (H1 - one per document)

## Major Section (H2)

### Subsection (H3)

#### Detail Section (H4)
```

### Code Blocks

Use fenced code blocks with language specification:

```markdown
```python
def hello():
    return "Hello, World!"
```

```bash
npm install
npm run build
```
```

### Lists

```markdown
Unordered lists:
- Item 1
- Item 2
  - Nested item
  - Another nested item

Ordered lists:
1. First step
2. Second step
3. Third step

Task lists:
- [x] Completed task
- [ ] Pending task
```

### Links and References

```markdown
# Inline links
[Link text](https://example.com)

# Reference links
[Link text][reference]
[reference]: https://example.com

# Relative links (preferred for repo files)
[Contributing Guide](../../CONTRIBUTING.md)
[Documentation](../../README.md)

# Anchor links
[Jump to section](#section-heading)
```

### Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Data 1   | Data 2   | Data 3   |
| Data 4   | Data 5   | Data 6   |

# Alignment
| Left | Center | Right |
|:-----|:------:|------:|
| L    | C      | R     |
```

### Images

```markdown
# Basic image
![Alt text](images/diagram.png)

# With title
![Alt text](images/diagram.png "Image title")

# Reference style
![Alt text][image-ref]
[image-ref]: images/diagram.png
```

## Technical Documentation

### API Documentation

```markdown
## API Reference

### GET /api/users/{id}

Retrieves a user by ID.

**Parameters:**

| Name | Type   | Required | Description        |
|------|--------|----------|---------------------|
| id   | string | Yes      | The user identifier |

**Response:**

```json
{
  "id": "123",
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Status Codes:**

| Code | Description           |
|------|-----------------------|
| 200  | Success               |
| 404  | User not found        |
| 500  | Internal server error |
```

### Decision Records (ADR)

```markdown
# ADR-001: Use PostgreSQL for User Data

## Status

Accepted

## Context

We need a database for storing user data with complex relationships.

## Decision

We will use PostgreSQL as our primary database.

## Consequences

### Positive
- Strong relational data support
- ACID compliance
- Wide ecosystem support

### Negative
- Requires more operational overhead than managed services
- Need expertise for optimization
```

## Best Practices

### General Guidelines

1. **Be concise** - Get to the point quickly
2. **Use active voice** - "Run the command" not "The command should be run"
3. **Include examples** - Show, don't just tell
4. **Keep it updated** - Stale docs are worse than no docs
5. **Use consistent formatting** - Same style throughout

### File Organization

```text
docs/
├── README.md           # Overview and navigation
├── getting-started.md  # Quick start guide
├── architecture.md     # System architecture
├── api/               # API documentation
│   ├── README.md
│   └── endpoints.md
├── guides/            # How-to guides
│   ├── deployment.md
│   └── contributing.md
└── adr/               # Architecture Decision Records
    ├── 001-database.md
    └── 002-framework.md
```

### Writing for Developers

1. **Start with the "why"** - Explain purpose before implementation
2. **Include prerequisites** - What they need before starting
3. **Provide copy-paste examples** - Working code snippets
4. **Explain error cases** - Common problems and solutions
5. **Link to related docs** - Don't repeat, reference

### Accessibility

1. **Use descriptive link text** - "See the guide" not "click here"
2. **Add alt text to images** - Describe what the image shows
3. **Use proper heading structure** - For screen readers
4. **Ensure sufficient contrast** - In any diagrams

## Common Patterns

### Collapsible Sections

```markdown
<details>
<summary>Click to expand</summary>

Hidden content here.

</details>
```

### Alerts/Callouts (GitHub)

```markdown
> [!NOTE]
> Important information users should know.

> [!TIP]
> Helpful advice for doing things better.

> [!WARNING]
> Critical content that could cause issues.

> [!CAUTION]
> Negative potential consequences of an action.
```

### Badges

```markdown
![Build Status](https://github.com/org/repo/workflows/CI/badge.svg)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/npm/v/package.svg)
```

## References

- [GitHub Markdown Guide](https://docs.github.com/en/get-started/writing-on-github)
- [ISE Documentation Practices](https://microsoft.github.io/code-with-engineering-playbook/documentation/)
- [Google Developer Documentation Style Guide](https://developers.google.com/style)
- [Microsoft Writing Style Guide](https://learn.microsoft.com/style-guide/welcome/)
