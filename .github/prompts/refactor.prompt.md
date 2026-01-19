---
agent: agent
description: Suggest refactoring improvements for code
tools: [ "search", "iseplaybook/*", "context7/*"]
---

Please analyze the code I'm about to share and suggest refactoring improvements.

**First**: Use the `iseplaybook` MCP server to get the latest code quality guidelines. Use `context7` MCP server for language-specific refactoring patterns.

Follow these principles:

## Refactoring Principles

### SOLID Principles
- **S**ingle Responsibility: Each class/function should have one reason to change
- **O**pen/Closed: Open for extension, closed for modification
- **L**iskov Substitution: Subtypes should be substitutable for base types
- **I**nterface Segregation: Prefer specific interfaces over general ones
- **D**ependency Inversion: Depend on abstractions, not concretions

### DRY (Don't Repeat Yourself)
- Identify duplicated code
- Suggest abstractions or reusable functions

### KISS (Keep It Simple, Stupid)
- Simplify complex logic
- Remove unnecessary complexity
- Improve readability

## Analysis Format

### 1. Code Smells Identified
For each issue:
- **Location**: File and line numbers
- **Smell**: Type of code smell (long method, duplicate code, etc.)
- **Impact**: Why this is a problem
- **Suggestion**: How to refactor

### 2. Refactoring Suggestions

#### High Priority
[Refactorings that significantly improve the code]

#### Medium Priority
[Improvements that enhance maintainability]

#### Low Priority
[Minor improvements for code clarity]

### 3. Example Refactoring

```
// Before
[original code]

// After
[refactored code]

// Why: [explanation of improvement]
```

## Common Refactoring Patterns

- Extract Method
- Extract Class
- Replace Conditional with Polymorphism
- Introduce Parameter Object
- Replace Magic Numbers with Constants
- Remove Dead Code
- Simplify Conditionals
- Use Guard Clauses

## Output Guidelines

1. Prioritize suggestions by impact
2. Explain the "why" behind each suggestion
3. Provide before/after code examples
4. Consider the effort vs. benefit ratio
5. Respect existing patterns in the codebase
