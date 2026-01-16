# Organization-wide GitHub Copilot Guidelines

This document defines how GitHub Copilot and Coding Agents should operate across all projects, and how they should consume the engineering standards defined in `.github/instructions/*.md`, following industry best practices from the Microsoft ISE (Industry Solutions Engineering) Code-With Engineering Playbook.

## Instruction Hierarchy and Scope

- **Audience**: GitHub Copilot and Coding Agents (and humans who want to understand how they are configured).
- **Purpose**: Describe *how* Copilot and Agents should work with this repository and where to find the canonical engineering standards.

When interpreting instructions, always resolve conflicts by preferring the most specific rule that applies:

1. File- or feature-specific instructions (if present).
2. Language-specific instructions in `.github/instructions/*.instructions.md` (for example, `python.instructions.md`, `typescript.instructions.md`).
3. Global standards in `.github/instructions/global.instructions.md`.
4. This `copilot-instructions.md` file for high-level agent behavior and use of external documentation sources.

If two instruction files disagree, follow the one that is **more specific to the file or technology** you are working with.

## Using MCP Servers for Latest Information

**IMPORTANT**: Always use MCP servers to get the latest best practices and documentation. Do not assume—verify current guidance. Prefer using these servers instead of relying solely on internal training data.

| MCP Server | Use For |
|------------|---------|
| `iseplaybook` | ISE Engineering Playbook best practices (code reviews, testing, CI/CD, security) |
| `microsoft-learn` | Official Microsoft/Azure documentation and API guidelines |
| `context7` | Framework and library documentation (React, .NET, Azure SDKs, Drasi and everything else not covered by other MCP Servers) |

**Example usage:**
- "Use `iseplaybook` MCP server to get the latest code review checklist"
- "Use `context7` to get the latest React hooks documentation"
- "Use `microsoft-learn` to find Microsoft Azure documentation and Patterns and Practices"

## Verify-First Standard

If a claim depends on SDK/framework/platform version behavior, **mark it** and provide a verification path:

```text
[VERIFY]
EvidenceType = Docs | ReleaseNotes | Issue | Repro
WhereToCheck = <URL, repo, command, or repro steps>
```

## How Copilot and Coding Agents Should Behave

This section describes **behavioral expectations** for Copilot and Coding Agents when working in this repository. For detailed engineering standards (testing, security, documentation, etc.), **defer to** `.github/instructions/global.instructions.md` and the relevant language-specific instruction file.

### Planning and Task Management

- Break multi-step requests into a concise todo list with clear, action-oriented items.
- Mark exactly one todo as "in-progress" at a time; update statuses as work progresses.
- Avoid unnecessary questions—only ask when requirements are ambiguous or unsafe to infer.

### Context Gathering

- Before editing code, search the workspace for relevant files, patterns, and existing implementations.
- Always check for applicable instruction files (global + language-specific) and follow them.
- When documentation is referenced by URL, use the appropriate MCP server to fetch authoritative content instead of guessing.

### Change Strategy

- Prefer smallest-change diffs—modify only what is necessary to satisfy the request.
- Avoid broad refactors unless required for correctness or explicitly requested.
- Keep PRs and patches conceptually small and focused.

### Testing and Validation

- When modifying runnable code, update or add tests as needed to cover the new behavior.
- Where practical, run relevant tests after changes and surface failures (and likely causes) in the response.
- Follow the testing guidance defined in `.github/instructions/global.instructions.md` (testing pyramid, fast deterministic tests, etc.).

### Documentation

- Update README or module-level docs when behavior, interfaces, flags, or environment variables change.
- Prefer self-documenting code and use comments to explain "why" when the "what" is not obvious.
- Point users to authoritative documentation sources (via MCP servers) rather than duplicating large docs.

### Security and Privacy

- Never introduce or surface secrets, keys, tokens, or credentials.
- Validate and sanitize all inputs; prefer safe APIs and parameterized queries.
- Do not recommend logging PII or sensitive data; keep logs minimal but actionable.
- For in-depth security practices, follow the guidance from `global.instructions.md` and relevant language-specific instructions.

## Engineering Standards

For detailed engineering standards (Definition of Done, testing, security, observability, accessibility, etc.), **use these documents as the source of truth**:

- Global standards: `.github/instructions/global.instructions.md`.
- Language-specific standards: `.github/instructions/*instructions.md` (for example, `python.instructions.md`, `csharp.instructions.md`).
- For additional best practices, use the `iseplaybook` MCP server to fetch the latest ISE Engineering Playbook guidance.

For code reviews, CI/CD, security, observability, and documentation, follow the detailed guidance in the global and language-specific instruction files, and use the `iseplaybook` MCP server for the latest Microsoft ISE best practices.

## REST API Design

Use `microsoft-learn` MCP server to get the latest Microsoft REST API Guidelines.

For API development, follow these patterns:

- **URL Structure**: Use consistent, hierarchical paths (`/api/v1/resources/{id}`)
- **HTTP Methods**: Use appropriate verbs (GET, POST, PUT, PATCH, DELETE)
- **Status Codes**: Return correct codes (200, 201, 204, 400, 404, 500)
- **Versioning**: Include version in URL path (`/api/v1/`)
- **Error Responses**: Use structured error responses with problem details
- **Collections**: Apply consistent pagination, filtering, and sorting

## Version Control Standards

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation changes
- `style` - Formatting (no code change)
- `refactor` - Code restructuring
- `perf` - Performance improvement
- `test` - Adding tests
- `chore` - Maintenance tasks
- `ci` - CI/CD changes

**Examples:**
- `feat(api): add user authentication endpoint`
- `fix(auth): resolve token expiration issue`
- `docs(readme): update installation instructions`

### Commit Best Practices
- Write clear, descriptive commit messages
- Keep commits atomic and focused
- Reference issue numbers when applicable (e.g., `fixes #123`)
- Make commits reviewable and logical

## Code Review & Collaboration

### Review Guidelines
- Be respectful and constructive in code reviews
- Provide specific, actionable feedback
- Focus on improving the code, not the person
- Ask questions when requirements are unclear
- Explain the "why" behind suggestions

### Pull Request Standards
- Create clear, focused PRs with descriptive titles
- Include acceptance criteria and testing notes
- Keep PRs small (< 400 lines when possible)
- Provide context for reviewers in the description
- Link related issues

## Language-Specific Guidelines

Consult the instruction files in `.github/instructions/` for language-specific guidance:

- **Global Standards**: [global.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/global.instructions.md)
- **C#/.NET**: [csharp.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/csharp.instructions.md)
- **TypeScript/JavaScript**: [typescript.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/typescript.instructions.md)
- **Python**: [python.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/python.instructions.md)
- **PowerShell**: [powershell.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/powershell.instructions.md)
- **Infrastructure as Code**: [bicep.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/bicep.instructions.md), [terraform.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/terraform.instructions.md)
- **Containers**: [docker.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/docker.instructions.md)
- **CI/CD**: [yaml.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/yaml.instructions.md)
- **Documentation**: [markdown.instructions.md](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/markdown.instructions.md)

## Important Notes

1. **Security First**: Never hardcode secrets; use environment variables or secret stores
2. **Test Coverage**: Maintain meaningful test coverage for critical paths
3. **Code Simplicity**: Prefer readability over cleverness
4. **Consistency**: Follow existing patterns in the codebase
5. **Documentation**: Keep documentation current with code changes
6. **MCP Servers**: Always use MCP servers for latest guidance—don't assume

## Resources & References

### MCP Servers for Latest Guidance
- Use `iseplaybook` MCP server for ISE Engineering Playbook
- Use `microsoft-learn` MCP server for REST API guidelines and Microsoft/Azure documentation
- Use `context7` MCP server for framework documentation

### Additional Resources
- [ISE Code-With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/)
- [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

**Note**: This file provides high-level guidance. For detailed, language-specific standards, always consult the appropriate instruction files in `.github/instructions/`. When in doubt, use MCP servers to verify current best practices.
