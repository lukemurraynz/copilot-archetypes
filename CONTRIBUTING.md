# Contributing to GitHub Copilot Archetype Standards

Thank you for your interest in contributing to this repository! This guide will help you add new standards, prompts, agents, and improvements.

## Table of Contents

- [Getting Started](#getting-started)
- [Adding New Content](#adding-new-content)
- [File Naming Conventions](#file-naming-conventions)
- [Front Matter Standards](#front-matter-standards)
- [Testing Your Changes](#testing-your-changes)
- [Submitting Changes](#submitting-changes)

## Getting Started

1. **Fork the repository** to your GitHub account
2. **Clone your fork** locally:
   ```powershell
   git clone https://github.com/YOUR-USERNAME/copilot-archetype-standards.git
   cd copilot-archetype-standards
   ```
3. **Create a feature branch**:
   ```powershell
   git checkout -b feature/your-feature-name
   ```

## Adding New Content

### Adding a New Instruction File

Instruction files define language or path-specific coding standards.

**Location**: `.github/instructions/`

**Naming**: `{language}.instructions.md` or `{framework}.instructions.md`

**Template**: Use `.github/templates/instructions.template.md`

**Key Sections:**
```markdown
---
applyTo: "**/*.{extension}"
description: Brief description of what this instruction file covers
---

# {Language/Framework} Development Standards

## Language/Framework Version
- Required version and key features

## Code Formatting & Linting
- Linter, formatter, type checking tools
- Configuration examples

## Modern Features
- Feature descriptions with code examples

## Code Style
- Naming conventions
- Project structure
- Common patterns

## Testing
- Framework and tools
- Best practices

## Resources
- Official documentation links
```

**Example**: See `.github/instructions/python.instructions.md`

### Adding a New Prompt File

Prompt files are reusable templates for common tasks with agent capabilities.

**Location**: `.github/prompts/`

**Naming**: 
- Language-specific: `{language}.{purpose}.prompt.md`
- Global: `global.{purpose}.prompt.md`

**Template**: Use `.github/templates/prompt.template.md`

**Key Sections:**
```markdown
---
mode: agent
description: Clear one-line description of what this prompt does
tools: [ "search", "iseplaybook/*", "context7/*"]
---

# {Purpose} Prompt

**Important**: Use MCP servers for latest guidance:
- `iseplaybook` for engineering best practices
- `context7` for framework-specific patterns
- `microsoftdocs` for Azure/Microsoft documentation

## Focus Areas
1. Area 1
2. Area 2
3. Area 3

## Process
- Step-by-step instructions
- Clear deliverables
- Output format expectations

## Examples
- Concrete usage examples
- Expected outputs
```

**Examples**: 
- See `.github/prompts/code-review.prompt.md`
- See `.github/prompts/test-generation.prompt.md`
- See `.github/prompts/refactor.prompt.md`

### Adding a New Agent

Agents define specialized workflows for development tasks.

**Location**: `.github/agents/`

**Naming**: `{language}.{mode}.agent.md`

**Template**: Use `.github/templates/agent.template.md`

**Key Sections:**
```markdown
---
name: agent-name
description: Brief description of the agent's purpose and approach
tools: [ "search", "github", "iseplaybook/*", "context7/*"]
---

# Agent Purpose

Role description with specific focus area.

**IMPORTANT**: Use MCP servers for latest guidance.

## Core Principles
- Principle 1
- Principle 2

## Review/Action Areas
- Detailed areas of focus
- Specific checks or actions

## Output Format
- How results are structured
- What to include
```

**Examples**:
- See `.github/agents/security-specialist.agent.md`
- See `.github/agents/azure-architect.agent.md`
- See `.github/agents/code-review.agent.md`

## File Naming Conventions

### Instruction Files
- Format: `{language}.instructions.md`
- Examples: `python.instructions.md`, `java.instructions.md`
- Location: `.github/instructions/`

### Prompt Files
- Format: `{purpose}.prompt.md` or `{scope}.{purpose}.prompt.md`
- Examples: `code-review.prompt.md`, `test-generation.prompt.md`, `refactor.prompt.md`
- Location: `.github/prompts/`

### Agent Files
- Format: `{purpose}.agent.md` or `{purpose}-specialist.agent.md`
- Examples: `security-specialist.agent.md`, `azure-architect.agent.md`, `code-review.agent.md`
- Location: `.github/agents/`


## Front Matter Standards

### Instruction Files
```yaml
---
applyTo: "**/*.{ext}"
description: One-line description
---
```

**Fields:**
- `applyTo` (required): Glob pattern for files this applies to
- `description` (required): Brief description of the instruction scope

### Prompt Files
```yaml
---
mode: agent
description: One-line description of what the prompt does
tools: [ "search", "iseplaybook/*", "context7/*"]
---
```

**Fields:**
- `mode` (required): Always `agent` for active prompts
- `description` (required): Clear, concise purpose statement
- `tools` (optional): Array of tools the prompt uses (common: read, search, iseplaybook, context7, microsoftdocs)

### Agent Files
```yaml
---
name: agent-name
description: One-line description of the agent
tools: [ "search", "github", "iseplaybook/*", "context7/*"]
---
```

**Fields:**
- `name` (required): Agent identifier (kebab-case)
- `description` (required): Purpose and approach of the agent
- `tools` (optional): Array of tools the agent uses

**Fields:**
- `description` (required): Brief description of the mode's purpose## Testing Your Changes

### Manual Testing

Validate your contributions before submitting:

### 1. Validate Front Matter

Ensure all YAML front matter is properly formatted:
- Check opening and closing `---` markers
- Verify required fields are present
- Ensure proper YAML syntax (quotes, brackets, colons)
- Test by opening in VS Code

### 2. Check Markdown Formatting

Verify markdown is well-formed:
- Use VS Code Markdown preview
- Check code fences are closed
- Verify links work
- Ensure proper heading hierarchy

### 3. Validate Links

Ensure all internal links use full GitHub URLs:
- ✅ Good: `https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/python.instructions.md`
- ❌ Bad: `../instructions/python.instructions.md`

### 4. Test in VS Code

1. Copy your changes to a test repository
2. Open the repository in VS Code with GitHub Copilot
3. Verify that:
   - Instruction files are applied correctly
   - Prompt files appear in Copilot Chat
   - Agents are accessible
   - Tools work as expected

### 5. Check Consistency

Compare your new file with existing similar files:
- Does it follow the same structure?
- Does it use consistent terminology?
- Does it reference appropriate related files?

## Submitting Changes

### 1. Commit Your Changes

Follow conventional commit format:
```powershell
git add .
git commit -m "feat: add refactoring prompt file

- Add refactor.prompt.md for common refactoring patterns
- Include examples for extract method, extract class
- Add references to ISE playbook"
```

**Commit message format:**
- `feat:` - New feature (new file, new section)
- `fix:` - Bug fix or correction
- `docs:` - Documentation updates
- `style:` - Formatting, no code change
- `refactor:` - Restructuring without changing behavior
- `test:` - Adding or updating tests

### 2. Push Your Branch

```powershell
git push origin feature/your-feature-name
```

### 3. Create Pull Request

1. Go to the repository on GitHub
2. Click "Pull Requests" > "New Pull Request"
3. Select your branch
4. Fill in the PR template:

```markdown
## Description
Brief description of what this PR adds or changes.

## Type of Change
- [ ] New instruction file
- [ ] New prompt file
- [ ] New agent
- [ ] Style guide update
- [ ] Documentation improvement
- [ ] Bug fix

## Files Added/Modified
- `.github/prompts/refactor.prompt.md` - Added
- `README.md` - Updated to reference new prompt

## Testing
- [x] Validated YAML front matter
- [x] Checked markdown formatting
- [x] Verified all links use full URLs
- [x] Tested in VS Code with Copilot
- [x] Compared with existing similar files

## Related Issues
Closes #123
```

### 4. Respond to Review Feedback

- Address all comments from reviewers
- Make requested changes in your branch
- Push updates (they'll appear in the PR automatically)

## Style and Quality Guidelines

### Writing Style
- **Clear and Concise**: Use simple language and short sentences
- **Actionable**: Provide specific examples and code snippets
- **Consistent**: Follow patterns from existing files
- **Complete**: Include all required sections

### Code Examples
- Use realistic, practical examples
- Show only ✅ secure/approved patterns (avoid including insecure examples)
- Include comments explaining why
- Test that examples are syntactically correct

### Documentation
- Link to official documentation when appropriate
- Use full GitHub URLs for internal references
- Keep examples up to date with current versions
- Include references to related files

## Need Help?

- **Questions**: Open a GitHub Discussion
- **Issues**: Create a GitHub Issue
- **General Help**: Contact the author and maintainer @lukemurraynz

## Code of Conduct

- Be respectful and constructive
- Focus on improving the content
- Provide clear, specific feedback
- Help others learn and contribute

## License

By contributing, you agree that your contributions will be licensed under the same license as this project (see LICENSE file).

---

Thank you for contributing to GitHub Copilot Archetype Standards! Your contributions help teams maintain consistent, high-quality code across their projects.
