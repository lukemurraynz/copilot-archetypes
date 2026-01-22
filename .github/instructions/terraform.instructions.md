---
applyTo: "**/*.tf,**/*.tfvars,**/*.terraform,**/*.tfstate,**/*.tflint.hcl,**/*.tf.json,**/*.tfvars.json"
description: "Terraform Infrastructure as Code best practices following ISE Engineering Playbook guidelines, Azure Verified Modules (AVM), and Azure security standards"
---

# Terraform Infrastructure Instructions

Follow HashiCorp Terraform best practices, Azure security guidance, and ISE Engineering Playbook standards.

**VERIFY-FIRST (MANDATORY):**

- Always validate assumptions using the `iseplaybook` and `microsoft-learn` MCP servers.
- Terraform, provider, and Azure behaviors are version-dependent — never assume defaults.
- If context is unclear (greenfield vs existing), ask before restructuring.

---

## Decision Gate: Greenfield vs Existing Infrastructure

Before generating or refactoring Terraform:

- **Greenfield**: New infrastructure with no prior state
- **Existing / Legacy**: Live infrastructure or established repository

Rules:

- Do **not** restructure existing repositories unless explicitly requested.
- For existing code, enhance incrementally and document deviations.
- For greenfield projects, follow recommended structure and defaults.

If unclear, ask the user.

---

## Azure Verified Modules (AVM) — Usage Policy

### Platform / Landing Zones / Shared Services

- **Prefer consuming Azure Verified Modules (AVM) directly**

### Productized Modules / Internal IP

- **Use AVM as reference patterns only**
- **Do NOT wrap or depend on AVM modules**

### Application Stacks

- Prefer AVM when available
- Otherwise implement resources directly using AVM patterns

---

## AVM Reference Usage Rules

When learning from AVM implementations, extract:

- Security defaults (TLS, encryption, network isolation)
- Variable validation patterns
- Dynamic blocks for optional features
- Output naming conventions
- Module testing and example structure

Do NOT blindly copy-paste; adapt intentionally and document decisions.

---

## Non-Negotiables

1. Never edit `.tfstate` or `.terraform/`
2. Never commit secrets or credentials
3. Always commit `.terraform.lock.hcl`
4. Authentication priority:
   1. Managed Identity
   2. OIDC / Federated Credentials
   3. Service Principal with certificate
   4. Service Principal with secret (last resort)
5. Make small, reversible changes only
6. `terraform fmt` and `terraform validate` are mandatory
7. Use `moved` blocks for renames
8. Use `import` blocks for brownfield adoption

---

## Security Defaults (REQUIRED)

Unless explicitly justified and documented:

- TLS 1.2 or higher
- HTTPS-only traffic
- `public_network_access_enabled = false`
- Encryption at rest and in transit
- Private Endpoints for PaaS services
- Least-privilege RBAC
- Diagnostic settings enabled with tiered approach

---

## Custom Module Requirements

When creating custom Terraform modules:

- Implement **actual Azure resources**, not module wrappers
- Follow AVM structural and security patterns
- Include **all required files**:

```text
modules/<module-name>/
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── README.md
└── examples/
    └── basic/
        ├── main.tf
        ├── variables.tf
        ├── outputs.tf
        ├── terraform.tfvars.example
        └── README.md
```

### Module Example Requirements

- `examples/` is **REQUIRED**
- Examples must be fully deployable
- Document example intent and tradeoffs

### Provider & Version Management

- Always verify latest provider versions before scaffolding
- Use pessimistic constraints (`~>`)
- Pin exact versions for production when required
- Perform provider/module upgrades in isolated PRs

#### Example:

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}
```

## State & Environment Isolation

- One state file per environment and blast-radius boundary
- Never mix environments in the same state or workspace
- Prefer CI identities (OIDC) for backend access
- Enforce locking and least-privilege access to state storage

## Refactoring Safety (Terraform ≥ 1.5)

Resource Renames

Use moved blocks to prevent destroy/recreate:

```hcl
moved {
  from = azurerm_resource_group.old
  to   = azurerm_resource_group.main
}
```

Brownfield Adoption

Use import blocks for existing resources:

```hcl
import {
  to = azurerm_resource_group.main
  id = "/subscriptions/<sub>/resourceGroups/<rg>"
}
```

Invariants

Use preconditions and postconditions to enforce safety:

```hcl
lifecycle {
  precondition {
    condition     = length(var.project) >= 3
    error_message = "Project name must be at least 3 characters."
  }
}
```

tfsec

---

## Validation Pipeline (Local + CI)

**Minimum:**

- `terraform fmt -recursive`
- `terraform validate`

**Recommended:**

- `tflint`
- `terraform plan -out=tfplan`
- `terraform show -json tfplan > tfplan.json`

**Rules:**

- Plans must be reviewed before apply
- Apply must be gated for production environments
- Store plan artifacts for traceability

---

## Security Scanning (Strongly Recommended)

Use at least one static analysis tool:

- tfsec
- Checkov
- CIS Azure Foundations Benchmark
- Azure Security Benchmark

**Rules:**

- Fail builds on critical findings
- Document accepted risks explicitly

---

## What NOT to Do

❌ Wrap AVM modules inside custom modules
❌ Hardcode credentials or secrets
❌ Enable public access by default
❌ Perform large refactors without approval
❌ Introduce breaking changes silently

---

## References

Azure Verified Modules: https://azure.github.io/Azure-Verified-Modules/
Terraform Documentation: https://developer.hashicorp.com/terraform
AzureRM Provider Docs: https://registry.terraform.io/providers/hashicorp/azurerm/latest
GitHub Copilot Instructions: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
