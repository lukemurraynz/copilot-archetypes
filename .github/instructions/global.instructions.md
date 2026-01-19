---
applyTo: "**/*"
description: Archetype-agnostic global development standards and Copilot guidance for all repositories
---

# Global Development Standards (Archetype‑Agnostic)

These are organization-wide rules that apply to every repository and project, regardless of language, framework, or archetype. Language- or tool-specific files may add details and can override sections explicitly for their scope. Copilot/Agents are configured via `.github/copilot-instructions.md`, which describes **how** they should apply these standards when working in this repository.

## Scope and Precedence

- Applies to all files in the repository unless a more specific instruction file overrides it (for example, Python/Java/Terraform instruction files).
- When guidance conflicts, prefer the most specific rule applicable to the file or technology in question.
- Keep changes minimal and focused; avoid broad refactors unless requested or strictly necessary.
- Operational behaviors for Copilot/Agents live in `.github/copilot-instructions.md`; this file defines engineering standards.

## Core Principles

- Small, focused changes with clear intent and atomic commits
- Clean, readable, maintainable code with meaningful names
- Single Responsibility per function/module where practical
- DRY: prefer reuse over duplication; extract helpers when duplication appears 3+ times
- Deterministic, reproducible builds and tests

## Definition of Done (DoD)

Before you consider a task complete, ensure:

1. Functionality implemented with clear inputs/outputs and errors handled
2. Automated tests added or updated (unit for pure logic; integration for I/O). Target at least a basic happy path and one edge/error case
3. Documentation updated (README, comments/docstrings, ADRs if design decisions changed)
4. Security and privacy concerns reviewed (no secrets; validated inputs; least-privileged access)
5. Lint/format/type checks pass locally and in CI
6. Commit message concise and descriptive; human-authored PRs include context, acceptance criteria, and risk notes

## Testing Standards

- Write tests alongside code changes; prefer fast, deterministic tests
- Organize tests by feature or module; keep fixtures small and explicit
- Mock external calls and system boundaries; avoid network/filesystem unless explicitly needed
- Include at least:
	- Happy path
	- One edge/boundary case
	- One failure/exception path
- Strive for incremental coverage improvement; do not reduce existing coverage without justification
- For TDD expectations, follow the workflow defined in `.github/copilot-instructions.md`

## Documentation Standards

- Update README or module-level docs when behavior, interfaces, flags, or environment variables change
- Use docstrings/comments to explain "why" when the "what" is not obvious
- Maintain ADRs for significant design or dependency choices
- Keep examples minimal and runnable

## Security and Compliance

- Never commit secrets, keys, tokens, or credentials; use secret managers and environment injection
- Validate and sanitize all inputs; use parameterized queries for data access
- Prefer least privilege for identities, roles, and API tokens
- Keep dependencies updated; run vulnerability scans and remediate critical/high findings
- Avoid insecure primitives: raw string SQL, eval/exec, shell with untrusted input, insecure random for security decisions
- Protect PII and sensitive data; log only what’s necessary and sanitize outputs

## Dependencies and Build

- Pin or bracket versions prudently to ensure reproducibility
- Prefer widely-used, actively maintained libraries
- Remove unused dependencies; avoid transitive bloat
- Keep builds cacheable; fail fast on lint/type errors
- Maintain supply-chain hygiene: prefer lockfiles and/or provenance/signing where supported

## Version Control and PR Hygiene

- Write atomic commits with imperative subject lines under ~72 chars
- Reference issue IDs where applicable
- Use branches for work; keep PRs small and focused with clear checklists
- In reviews, be kind, specific, and actionable; request changes with concrete suggestions

## Observability and Operations

- Use structured logging; include request correlation IDs at boundaries
- Avoid logging secrets and sensitive payloads
- Fail with actionable errors; include context for troubleshooting
- Consider basic performance characteristics and document any non-obvious tradeoffs

## Accessibility and Inclusion

- Write inclusive language in code, docs, and UI strings
- Ensure docs and examples are accessible (headings, links, contrast in images)

## Working with Copilot/Agents

- Leverage repository prompts and agents when available for planning, security review, and test generation.
- For detailed Copilot/Agents behavior (instruction hierarchy, MCP usage, task planning), see `.github/copilot-instructions.md`.
- Reference prompt and agent files using the full URLs above when embedding links in Markdown.
- Prefer minimal, targeted suggestions; validate generated code (human- or tool-authored) with tests and linters.
- Use minimal context: read only files needed to complete the task, and reuse existing patterns before creating new ones.
- If verification fails, mark the response as `[UNCERTAIN]` and defer to human review.

---

By following these global standards, teams maintain consistency across repositories while still allowing language- and framework-specific instruction files to tailor the details where appropriate.
