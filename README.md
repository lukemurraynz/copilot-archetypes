# GitHub Copilot Archetype Standards

A centralized repository containing development standards, coding guidelines, and GitHub Copilot configuration files following Microsoft ISE Engineering Playbook best practices.

## 📋 Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Repository Structure](#repository-structure)
- [Supported Languages & Frameworks](#supported-languages--frameworks)
- [GitHub Copilot Integration](#github-copilot-integration)
- [Understanding Instruction Files](#️-understanding-instruction-files)
- [How to Use](#how-to-use)
- [Testing the Configuration](#testing-the-configuration)
- [Contributing](#contributing)
- [Additional Resources](#additional-resources)

## 🎯 Overview

This repository serves as a central source of truth for:

- **Organisation-wide coding standards** following Microsoft ISE Code-With Engineering Playbook
- **GitHub Copilot instruction files** that guide AI-assisted development
- **Reusable prompt templates** for common development tasks
- **Custom agents** for specialized workflows (security reviews, code reviews, architecture planning)
- **Chat modes** for focused development sessions
- **Language-specific style guides** for 10+ languages and frameworks

Also see [VS Code Customization](https://code.visualstudio.com/docs/copilot/customization/overview) for more about GitHub Copilot customization.

### Why This Repository?

GitHub Copilot custom instruction files, prompt files, and agents cannot be centrally managed, even in GitHub Enterprise Cloud (GHEC). This repository provides a workaround by:

1. **Centralized Standards**: Store all standards and instructions in one location
2. **Consistent Experience**: Provide the same development guidance across all projects
3. **Easy Distribution**: Reference these standards via direct integration or Markdown links
4. **Simplified Updates**: Update once, propagate to all referencing repositories
5. **Template Library**: Reusable templates for creating new instruction files, prompts, and agents

## 🚀 Quick Start

### Option 1: Direct Integration (Recommended)

Copy the `.github` directory structure into your project:

```powershell
# Clone this repository
git clone https://github.com/lukemurraynz/copilot-archetype-standards.git

# Copy .github directory to your project
Copy-Item -Recurse copilot-archetype-standards\.github\ C:\path\to\your\project\
```

### Option 2: Git Submodule

Add this repository as a submodule:

```powershell
cd C:\path\to\your\project
git submodule add https://github.com/lukemurraynz/copilot-archetype-standards.git standards
git commit -m "Add copilot standards as submodule"
```

### Option 3: Reference via Links (Experimental)

Create lightweight instruction files that reference this repository. See [How to Use](#how-to-use) section for details.

## 📁 Repository Structure

```
.
├── README.md
├── CONTRIBUTING.md                          # Guide for adding new content
├── .github/
│   ├── copilot-instructions.md              # Repository-wide high-level rules
│   │
│   ├── instructions/                        # Path-scoped instruction files
│   │   ├── global.instructions.md           # Archetype-agnostic global standards
│   │   ├── bicep.instructions.md            # Azure Bicep IaC standards
│   │   ├── csharp.instructions.md           # C# and .NET development
│   │   ├── docker.instructions.md           # Docker and container practices
│   │   ├── markdown.instructions.md         # Documentation standards
│   │   ├── powershell.instructions.md       # PowerShell scripting
│   │   ├── python.instructions.md           # Python 3.12+ standards
│   │   ├── terraform.instructions.md        # Terraform 1.13+ with Azure
│   │   ├── typescript.instructions.md       # TypeScript/JavaScript standards
│   │   └── yaml.instructions.md             # YAML and CI/CD pipelines
│   │
│   ├── prompts/                             # Reusable prompt templates
│   │   ├── code-review.prompt.md            # Code review workflows
│   │   ├── refactor.prompt.md               # Refactoring assistance
│   │   └── test-generation.prompt.md        # Test generation workflows
│   │
│   ├── agents/                              # Custom specialized agents
│   │   ├── azure-architect.agent.md         # Azure architecture planning
│   │   ├── backlog-refinement.agent.md      # Story refinement & planning
│   │   ├── cleanup-specialist.agent.md      # Code cleanup & optimization
│   │   ├── code-review.agent.md             # Comprehensive code reviews
│   │   ├── readme-specialist.agent.md       # Documentation generation
│   │   ├── security-specialist.agent.md     # Security vulnerability scanning
│   │   └── troubleshooting-specialist.agent.md # Debugging assistance
│   │
│   ├── skills/                              # Reusable skills
│   │   └── drasi-queries/                   # Drasi query templates
│   │
│   └── templates/                           # Templates for new content
│       ├── agent.template.md                # Agent template
│       ├── instructions.template.md         # Instruction file template
│       ├── prompt.template.md               # Prompt template
│       └── README.md                        # Template usage guide
```

### File Types and Purposes

#### `.github/copilot-instructions.md`
- **Purpose**: Repository-wide high-level coding rules and MCP server integration guidance
- **Scope**: Automatically applied to entire repository
- **Support**: VS Code, GitHub Copilot
- **Key Features**: Links to ISE Engineering Playbook via MCP servers

#### `.github/instructions/*.instructions.md`
- **Purpose**: Language or path-specific instructions
- **Scope**: Applied to files matching `applyTo` glob patterns
- **Support**: VS Code, Copilot Edits, GitHub Copilot
- **Format**: Front matter with `applyTo` and `description` fields
- **Coverage**: 10 languages/frameworks (Bicep, C#, Docker, Markdown, PowerShell, Python, Terraform, TypeScript, YAML, Global)

#### `.github/prompts/*.prompt.md`
- **Purpose**: Reusable prompt templates for common development tasks
- **Scope**: Workspace or user-scoped
- **Support**: GitHub Copilot Chat
- **Format**: Front matter with description and optional tool specifications
- **Available Prompts**: Code review, refactoring, test generation

#### `.github/agents/*.agent.md`
- **Purpose**: Custom agents for specialized workflows
- **Scope**: Workspace-scoped
- **Support**: GitHub Copilot Chat
- **Format**: Front matter with `description` and `tools` fields
- **Available Agents**: 7 specialized agents for architecture, security, code review, documentation, cleanup, troubleshooting, and backlog refinement

#### `.github/templates/`
- **Purpose**: Templates for creating new instruction files, prompts, and agents
- **Usage**: Copy and customize templates when adding new content
- **Documentation**: See templates/README.md for detailed usage 

## �️ Supported Languages & Frameworks

This repository provides comprehensive standards for:

### Languages
- **Python** (>=3.12) - Type hints, ruff linting, black formatting, pytest testing
- **C#/.NET** - .NET 8+, proper async/await, dependency injection patterns
- **TypeScript/JavaScript** - Modern ES standards, type safety, testing practices
- **PowerShell** (>=7.0) - Script structure, error handling, approved verbs

### Infrastructure as Code
- **Terraform** (>=1.13) - Azure-focused, pinned providers, tfsec/checkov scanning
- **Bicep** - Azure native IaC, modular design, best practices
- **Docker** - Multi-stage builds, security scanning, optimization

### Configuration & Documentation
- **YAML** - CI/CD pipelines, GitHub Actions, Azure DevOps
- **Markdown** - README structure, documentation standards, ADRs

### Cross-Cutting Concerns
- **Global Standards** - SOLID principles, DRY, security, testing, code review practices
- **MCP Integration** - ISE Playbook, Context7, Microsoft Docs, API Guidelines

Each language/framework includes:
- ✅ Instruction files with coding standards
- ✅ Specialized agents for workflows
- ✅ Integration with ISE Engineering Playbook via MCP servers
- ✅ Examples and best practices

## 🤖 GitHub Copilot Integration

### VS Code Setup

1. **Install GitHub Copilot Extensions**
   - GitHub Copilot (code suggestions)
   - GitHub Copilot Chat (conversational AI)

2. **Instruction Files Auto-Load**
   - `copilot-instructions.md` applies repository-wide
   - `instructions/*.instructions.md` applies to matching file patterns
   - Automatically activated when editing relevant files

3. **Using Prompts**
   ```
   # In Copilot Chat
   /prompt code-review
   /prompt test-generation
   /prompt refactor
   ```

4. **Activating Agents**
   ```
   # In Copilot Chat
   @azure-architect Help me design a microservices architecture
   @security-specialist Review this code for OWASP vulnerabilities
   @readme-specialist Generate documentation for this project
   ```

### How Copilot Uses These Files

| File Type | When Applied | Example |
|-----------|--------------|---------|
| `copilot-instructions.md` | All Copilot interactions | MCP server usage, commit conventions |
| `*.instructions.md` | Files matching `applyTo` pattern | Python files get Python standards |
| `*.prompt.md` | User invokes via `/prompt` | `/prompt code-review` |
| `*.agent.md` | User mentions via `@agent` | `@security-specialist` |

### Available Agents

| Agent | Purpose | Key Features |
|-------|---------|--------------|
| **@azure-architect** | Azure solution design | Architecture patterns, service selection, cost optimization |
| **@security-specialist** | Security reviews | OWASP Top 10, vulnerability scanning, secure coding |
| **@code-review** | Code quality reviews | Best practices, maintainability, performance |
| **@readme-specialist** | Documentation | README generation, structure, C4 diagrams |
| **@cleanup-specialist** | Code optimization | Refactoring, tech debt reduction, code smells |
| **@troubleshooting-specialist** | Debugging | Root cause analysis, fix suggestions |
| **@backlog-refinement** | Story refinement | User stories, acceptance criteria, task breakdown |

### MCP Server Integration

This repository integrates with MCP (Model Context Protocol) servers for up-to-date guidance:

```markdown
# Example: Get latest Python testing practices
Use iseplaybook MCP server to get current pytest best practices

# Example: Get Azure SDK documentation
Use context7 MCP server for Azure SDK for Python latest patterns
```

**Available MCP Servers:**
- `iseplaybook` - ISE Engineering Playbook (code reviews, testing, CI/CD, security)
- `context7` - Framework documentation (React, .NET, Azure SDKs)
- `microsoftdocs` - Official Microsoft/Azure documentation
- `api-guidelines-docs` - Microsoft REST API Guidelines

## �️ Understanding Instruction Files

This repository uses a layered instruction system to guide GitHub Copilot and Coding Agents while maintaining clear, consistent standards across all projects.

### How the Instruction Files Work Together

#### `.github/copilot-instructions.md` - Agent Behavior

**Purpose:** Defines *how* GitHub Copilot and Coding Agents should operate in this repository.

**What it contains:**
- MCP server usage guidelines (when to fetch latest docs)
- Instruction file hierarchy and conflict resolution rules
- Behavioral expectations (planning, context gathering, testing)
- Pointers to detailed engineering standards

**Example:** "Use `iseplaybook` MCP server for latest testing best practices" or "Break multi-step tasks into a todo list."

#### `.github/instructions/global.instructions.md` - Engineering Standards

**Purpose:** The canonical source of truth for *what good engineering looks like* across all projects.

**What it contains:**
- Definition of Done checklist
- Testing pyramid and standards
- Security and compliance requirements
- Documentation expectations
- Version control and PR hygiene

**Example:** "Write atomic commits with imperative subject lines" or "Include at least happy path, edge case, and failure path tests."

#### `.github/instructions/*.instructions.md` - Language-Specific Standards

**Purpose:** Detailed coding standards for specific languages and frameworks.

**What they contain:**
- Language-specific syntax and style guidelines
- Framework best practices
- Tool recommendations (linters, formatters, test frameworks)
- Common patterns and anti-patterns

**Example:** Python requires type hints, 120-character lines, and pytest. C# uses async/await patterns and dependency injection.

### Instruction Hierarchy (Conflict Resolution)

When multiple instruction files provide guidance on the same topic, follow this precedence order:

1. **File-specific or feature-specific instructions** (if present)
2. **Language-specific instructions** (e.g., `python.instructions.md`)
3. **Global standards** (`global.instructions.md`)
4. **Copilot behavioral guidelines** (`copilot-instructions.md`)

**Rule:** Always follow the **most specific** instruction that applies to your current context.

### For Contributors: Which File to Edit?

| What You're Changing | Edit This File |
|----------------------|----------------|
| How Copilot/Agent should behave (planning, context gathering, MCP usage) | `copilot-instructions.md` |
| Organization-wide engineering standards (DoD, security, testing philosophy) | `global.instructions.md` |
| Python/C#/TypeScript/etc. specific coding rules | Language-specific `.instructions.md` |
| New language support | Create new `{language}.instructions.md` |

### Example: How Copilot Uses These Files

When working on a Python file:

1. **Copilot reads** `copilot-instructions.md` → "Use MCP servers for latest info; plan work with todos"
2. **Copilot reads** `python.instructions.md` → "Use type hints, ruff linting, pytest"
3. **Copilot reads** `global.instructions.md` → "Write tests (happy path + edge case), validate inputs, atomic commits"
4. **Copilot applies** all three layers to generate Python code with proper types, tests, and commit messages

### Why This Structure?

**Benefits:**

- ✅ **No duplication:** Standards defined once, referenced everywhere
- ✅ **Clear ownership:** Know exactly where to update rules
- ✅ **Consistent behavior:** All projects follow same agent patterns
- ✅ **Easy maintenance:** Update global standards in one place
- ✅ **Language flexibility:** Override global rules for language-specific needs

**Example Scenario:**

A developer asks Copilot to create a Python authentication function:

- **`copilot-instructions.md`** → Copilot uses `iseplaybook` to fetch latest security guidance
- **`python.instructions.md`** → Copilot generates with type hints, follows PEP 8
- **`global.instructions.md`** → Copilot adds tests (happy path + failure case), validates inputs

Result: Secure, well-tested, properly typed Python code following all applicable standards.

## �📖 How to Use

### For New Projects

Choose one of these integration methods:

#### Option 1: Direct Integration (Recommended)

Copy the entire `.github` directory into your project:

```powershell
# Clone this repository
git clone https://github.com/lukemurraynz/copilot-archetype-standards.git

# Copy .github directory to your project
Copy-Item -Recurse copilot-archetype-standards\.github\ C:\path\to\your\project\.github\

# Commit the changes
cd C:\path\to\your\project
git add .github/
git commit -m "Add GitHub Copilot archetype standards"
```

**Benefits:**
- ✅ Full offline access to all standards
- ✅ Works immediately without external dependencies
- ✅ Can customize for project-specific needs
- ✅ Version controlled with your project

#### Option 2: Git Submodule (Advanced)

Reference this repository as a submodule:

```powershell
cd C:\path\to\your\project
git submodule add https://github.com/lukemurraynz/copilot-archetype-standards.git .github/standards
git commit -m "Add copilot standards as submodule"
```

Then create symbolic links or reference files:
```powershell
# Create junction (Windows)
New-Item -ItemType Junction -Path ".github\instructions" -Target ".github\standards\.github\instructions"
```

**Benefits:**
- ✅ Always up-to-date with central repository
- ✅ Smaller repository footprint
- ⚠️ Requires submodule initialization for new clones

#### Option 3: Selective Files

Copy only the files you need:

```powershell
# Example: Python project
Copy-Item copilot-archetype-standards\.github\copilot-instructions.md .github\
Copy-Item copilot-archetype-standards\.github\instructions\global.instructions.md .github\instructions\
Copy-Item copilot-archetype-standards\.github\instructions\python.instructions.md .github\instructions\
Copy-Item copilot-archetype-standards\.github\agents\security-specialist.agent.md .github\agents\
```

**Benefits:**
- ✅ Minimal file footprint
- ✅ Only includes relevant standards
- ⚠️ Manual updates required

### For Existing Projects

1. **Audit Current Standards**
   - Review existing `.github` configuration
   - Identify conflicts with archetype standards

2. **Merge or Replace**
   - **Merge**: Combine project-specific rules with archetype standards
   - **Replace**: Adopt archetype standards fully

3. **Test Integration**
   - Open project in VS Code
   - Verify Copilot applies standards correctly
   - Test agents and prompts

4. **Customize as Needed**
   - Add project-specific rules to `copilot-instructions.md`
   - Override specific instruction files if necessary
   - Document deviations in project README

### Reference via Links (Experimental)

Create lightweight files that reference this repository:

**Example: `.github/copilot-instructions.md` in your project**
```markdown
# Project Copilot Instructions

Follow organisation standards from central repository:
https://github.com/lukemurraynz/copilot-archetype-standards

## Language-Specific Standards

- [Python Standards](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/python.instructions.md)
- [Terraform Standards](https://github.com/lukemurraynz/copilot-archetype-standards/tree/master/.github/instructions/terraform.instructions.md)

## Project-Specific Rules

[Your project-specific guidelines here]
```

**Note**: Effectiveness depends on GitHub Copilot's ability to follow external links. Test thoroughly.

### Example Project Structures

#### Python Project
```
python-project/
├── src/
│   └── myapp/
│       ├── __init__.py
│       └── main.py
├── tests/
│   └── test_main.py
├── pyproject.toml
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   ├── global.instructions.md
│   │   └── python.instructions.md
│   └── agents/
│       └── security-specialist.agent.md
└── README.md
```

#### Terraform Project
```
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── modules/
│   └── networking/
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   ├── global.instructions.md
│   │   └── terraform.instructions.md
│   └── agents/
│       ├── azure-architect.agent.md
│       └── security-specialist.agent.md
└── README.md
```

#### .NET Project
```
dotnet-project/
├── src/
│   └── MyApp/
│       ├── Controllers/
│       ├── Services/
│       └── Program.cs
├── tests/
│   └── MyApp.Tests/
├── MyApp.sln
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   ├── global.instructions.md
│   │   └── csharp.instructions.md
│   └── agents/
│       └── code-review.agent.md
└── README.md
```

## 🧪 Testing the Configuration

### Quick Verification

After integrating the standards, verify Copilot is using them:

1. **Open VS Code** in your project directory

2. **Test Instruction Application**
   - Create a new file matching a pattern (e.g., `test.py`)
   - Ask Copilot to generate code
   - Verify it follows standards (type hints, formatting, etc.)

3. **Test Prompts**
   ```
   # In Copilot Chat
   /prompt code-review
   ```
   - Should show the code review prompt template

4. **Test Agents**
   ```
   # In Copilot Chat
   @security-specialist Review this code
   ```
   - Should activate the security specialist agent

### Detailed Test Plan

For thorough testing across archetypes:

#### Test 1: Instruction Following

**Python Test:**
```powershell
# Create test file
echo "# Ask Copilot to create a user authentication function" > test.py
code test.py
```

**Expected Behavior:**
- ✅ Suggests type hints (`def authenticate(username: str, password: str) -> bool:`)
- ✅ Uses 120-character line length
- ✅ Follows PEP 8 conventions
- ✅ Suggests pytest for testing

**C# Test:**
```powershell
echo "// Ask Copilot to create a user service" > UserService.cs
code UserService.cs
```

**Expected Behavior:**
- ✅ Suggests async/await patterns
- ✅ Uses dependency injection
- ✅ Proper error handling
- ✅ XML documentation comments

#### Test 2: Agent Activation

```
# In Copilot Chat
@security-specialist Review the authentication logic in UserService.cs
```

**Expected Output:**
- Security checklist (OWASP Top 10)
- Specific vulnerabilities found
- Remediation recommendations
- Code examples for fixes

#### Test 3: Prompt Usage

```
# In Copilot Chat
/prompt test-generation

Select the authenticate function
```

**Expected Output:**
- Test file scaffolding
- Happy path tests
- Edge case tests
- Mock/fixture setup

#### Test 4: MCP Integration

```
# In Copilot Chat
Use iseplaybook MCP server to get the latest code review checklist
```

**Expected Behavior:**
- Fetches current ISE playbook guidance
- Provides up-to-date best practices
- References specific playbook sections

## 🤝 Contributing

We welcome contributions! Help us expand and improve these archetype standards.

### Quick Start

1. **Fork this repository**
2. **Create a feature branch**: `git checkout -b feature/your-feature`
3. **Make your changes** following existing patterns
4. **Test in VS Code** with GitHub Copilot
5. **Submit a pull request** with clear description

### What You Can Contribute

#### 1. New Instruction Files
Add support for new languages or frameworks:
- Use `templates/instructions.template.md` as starting point
- Follow existing naming: `{language}.instructions.md`
- Include proper front matter with `applyTo` glob pattern
- Document language-specific best practices

**Example:**
```yaml
---
applyTo: "**/*.go"
description: 'Go language development best practices'
---
```

#### 2. New Prompts
Create reusable workflow templates:
- Use `templates/prompt.template.md` as starting point
- Follow naming: `{scope}.{purpose}.prompt.md`
- Include clear instructions and examples
- Test with actual code scenarios

#### 3. New Agents
Build specialized workflow agents:
- Use `templates/agent.template.md` as starting point
- Follow naming: `{purpose}.agent.md`
- Define clear agent purpose and capabilities
- Specify required tools

#### 4. Improve Documentation
- Fix typos or unclear instructions
- Add examples and use cases
- Update outdated information
- Add troubleshooting guidance

### File Naming Conventions

| Type | Format | Example |
|------|--------|---------|
| Instruction | `{language}.instructions.md` | `python.instructions.md`, `rust.instructions.md` |
| Prompt | `{scope}.{purpose}.prompt.md` | `code-review.prompt.md`, `refactor.prompt.md` |
| Agent | `{purpose}.agent.md` | `security-specialist.agent.md` |
| Chat Mode | `{mode}.chatmode.md` | `rtrt.chatmode.md` |

### Front Matter Requirements

All contribution files must include proper YAML front matter:

**Instructions:**
```yaml
---
applyTo: "**/*.{ext}"
description: Brief description
---
```

**Prompts:**
```yaml
---
description: Brief description
---
```

**Agents:**
```yaml
---
description: Brief description
tools: [search, usages, githubRepo]  # Optional
---
```

### Testing Your Contributions

Before submitting a PR:

1. **Validate Syntax**
   - ✅ YAML front matter is valid
   - ✅ Markdown formatting is correct
   - ✅ All links work (use full GitHub URLs)

2. **Test in VS Code**
   - ✅ Instruction files load correctly
   - ✅ Prompts appear in Copilot Chat
   - ✅ Agents activate properly
   - ✅ Expected behavior occurs

3. **Review Checklist**
   - ✅ Follows existing file structure
   - ✅ Uses consistent terminology
   - ✅ Includes examples where helpful
   - ✅ Documents any new patterns

4. **Compare with Similar Files**
   - Look at existing instruction files in same category
   - Follow established patterns and style
   - Maintain consistency across repository

### Adding New Language Archetypes

To add comprehensive support for a new language:

1. **Create instruction file**
   - Location: `.github/instructions/{language}.instructions.md`
   - Include: Coding standards, tool recommendations, testing practices
   - Reference: ISE Playbook and language-specific best practices

2. **Create specialized prompts** (optional)
   - Test generation prompt
   - Security review prompt
   - Module/class generation prompt

3. **Create agents** (optional)
   - Language-specific planner agent
   - Security reviewer agent

4. **Update main README**
   - Add language to "Supported Languages" section
   - Include example project structure
   - Add to file naming conventions

5. **Provide examples**
   - Sample code following standards
   - Common patterns and anti-patterns
   - Integration examples

### Code Review Process

All contributions go through review:

1. **Automated Checks**
   - Markdown linting
   - YAML validation
   - Link checking

2. **Manual Review**
   - Alignment with ISE Playbook
   - Consistency with existing standards
   - Completeness and clarity
   - Practical applicability

3. **Testing**
   - Reviewers test in VS Code
   - Verify Copilot integration
   - Validate expected behavior

### Getting Help

- 📖 Read [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines
- 💬 Open a discussion for questions
- 🐛 Report issues for bugs or problems
- 💡 Suggest enhancements via issues

### Recognition

Contributors will be:
- Listed in repository contributors
- Mentioned in release notes for significant contributions
- Credited in documentation they improve

## 📖 Additional Resources

### GitHub Copilot Documentation
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Copilot Chat Documentation](https://docs.github.com/en/copilot/github-copilot-chat)
- [VS Code Copilot Customization](https://code.visualstudio.com/docs/copilot/copilot-customization)
- [Copilot Extensions](https://docs.github.com/en/copilot/using-github-copilot/using-extensions-to-integrate-external-tools-with-copilot-chat)

### Microsoft Engineering Playbook
- [ISE Code-With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/)
- [Code Reviews](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/)
- [Automated Testing](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/)
- [CI/CD](https://microsoft.github.io/code-with-engineering-playbook/continuous-integration/)
- [Security](https://microsoft.github.io/code-with-engineering-playbook/security/)

### API & Architecture Guidelines
- [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)
- [Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
- [Cloud Design Patterns](https://learn.microsoft.com/azure/architecture/patterns/)
- [Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)

### Security Resources
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [Azure Security Best Practices](https://learn.microsoft.com/azure/security/fundamentals/best-practices-and-patterns)

### Language-Specific Resources
- **Python**: [PEP 8](https://peps.python.org/pep-0008/), [Type Hints (PEP 484)](https://peps.python.org/pep-0484/)
- **C#/.NET**: [.NET Coding Conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- **TypeScript**: [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- **Terraform**: [Terraform Best Practices](https://www.terraform-best-practices.com/)
- **PowerShell**: [PowerShell Best Practices](https://learn.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-development-guidelines)

## 📄 License

This project is licensed under the terms included in the LICENSE file in the root directory.

## 🙋 Support

### Get Help

- 💬 **Discussions**: Use [GitHub Discussions](https://github.com/lukemurraynz/copilot-archetype-standards/discussions) for questions
- 🐛 **Issues**: Report bugs or request features via [GitHub Issues](https://github.com/lukemurraynz/copilot-archetype-standards/issues)
- 📖 **Documentation**: Review existing docs and instruction files
- 🔍 **Search**: Check existing issues and discussions first

### Contact

- **GitHub**: [@lukemurraynz](https://github.com/lukemurraynz)
- **Repository**: [copilot-archetype-standards](https://github.com/lukemurraynz/copilot-archetype-standards)

### Troubleshooting

**Copilot not applying instructions?**
- Verify `.github` folder structure is correct
- Check YAML front matter syntax
- Reload VS Code window (Ctrl+Shift+P > "Reload Window")
- Review Copilot output logs

**Agents not activating?**
- Ensure GitHub Copilot Chat extension is installed
- Check agent file has proper front matter
- Use `@agent-name` syntax in chat
- Verify agent file location (`.github/agents/`)

**Prompts not showing?**
- Check prompt file location (`.github/prompts/`)
- Validate YAML front matter
- Use `/prompt` command in Copilot Chat
- Reload VS Code if recently added

---

**Last Updated**: 2025-12-22
**Maintained By**: [@lukemurraynz](https://github.com/lukemurraynz)
**Version**: 2.0
**Repository**: [github.com/lukemurraynz/copilot-archetype-standards](https://github.com/lukemurraynz/copilot-archetype-standards)