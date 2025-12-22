---
mode: agent
description: Request a thorough code review of changes
tools: [ "search", "iseplaybook/*", "context7/*"]
---

Please review the code changes I'm about to share. 

**First**: Use the `iseplaybook` MCP server to get the latest code review checklist for the relevant language. Use `context7` MCP server for framework-specific best practices.

Focus on:

1. **Correctness** - Does the code work as intended?
2. **Security** - Are there any vulnerabilities?
3. **Performance** - Are there obvious performance issues?
4. **Maintainability** - Is the code readable and well-structured?
5. **Testing** - Is test coverage adequate?

For each issue found, please:
- Explain what the problem is
- Explain why it matters
- Suggest how to fix it
- Provide a code example if helpful

Use this format for your feedback:

## Critical Issues 🔴
[Must fix before merging]

## Suggestions 🟡
[Should consider]

## Minor Notes 🟢
[Nice to have]

## What's Good ✅
[Positive observations]
