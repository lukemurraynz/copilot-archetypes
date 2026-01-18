---
applyTo: "**/*.tf,**/*.tfvars,**/*.terraform,**/*.tfstate,**/*.tflint.hcl,**/*.tf.json,**/*.tfvars.json"
description: 'Terraform Infrastructure as Code best practices following ISE Engineering Playbook guidelines, including Azure Verified Modules (AVM)'
---

# Terraform Infrastructure Instructions

Follow HashiCorp best practices and ISE Terraform guidelines.

**IMPORTANT**: Always use the `iseplaybook` and `microsoft-learn` MCP servers to get the latest best practices and documentation. Do not assume—verify current guidance before implementing.

## Azure Verified Modules (PREFERRED)

**ALWAYS prefer Azure Verified Modules (AVM)** over writing custom Terraform modules from scratch.

Use the `microsoft-learn` MCP server to search for the latest Azure Verified Modules:
- Search: `Azure Verified Modules Terraform <resource-type>`

### What are Azure Verified Modules?

Azure Verified Modules (AVM) are pre-built, tested, and validated Terraform and Bicep modules that follow Azure best practices. These modules enable you to create, update, or review Azure Infrastructure as Code (IaC) with confidence.

Azure Verified Modules are Microsoft-maintained, well-tested Terraform modules that:
- Follow Azure best practices by default
- Are regularly updated and maintained
- Include comprehensive security configurations
- Provide consistent interfaces across resources
- Are validated through rigorous testing and compliance checks

### Critical: AVM PR Validation Requirements

**IMPORTANT**: When working in AVM repositories, the following local unit tests MUST be executed to comply with PR checks. Failure to run these tests will cause PR validation failures. AVM testing also includes additional tools (avmfix, terraform-docs, Conftest/OPA, custom TFLint rules) that run centrally in GitHub workflows; treat the commands below as the **minimum local checks**, and always review PR CI results for the full test matrix:

```bash
./avm pre-commit
./avm tflint
./avm pr-check
```

For AVM contributors and module authors, **SHOULD** run module unit tests via:

```bash
./avm unit-test
```

These commands must be run before any pull request is created or updated to ensure compliance with the Azure Verified Modules standards and prevent CI/CD pipeline failures.

More details: [AVM Contribution Documentation](https://azure.github.io/Azure-Verified-Modules/contributing/terraform/testing/)

**Failure to run these tests will cause PR validation failures and prevent successful merges.**

### Using AVM in Terraform

```hcl
# ✅ PREFERRED: Use Azure Verified Module from Terraform Registry
module "storage_account" {
  source  = "Azure/avm-res-storage-storageaccount/azurerm"
  version = "~> 0.1"

  enable_telemetry    = true
  name                = var.storage_account_name
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  # AVM handles security best practices automatically
}

# ❌ AVOID: Writing custom resources when AVM exists
resource "azurerm_storage_account" "main" {
  name                     = var.storage_account_name
  # Manually configuring everything...
}
```

### Module Discovery

#### Terraform Registry

- Search for "avm" + resource name
- Filter by "Partner" tag to find official AVM modules
- Example: Search "avm storage account" → filter by Partner

#### Official AVM Index

> **Note:** The following links always point to the latest version of the CSV files on the main branch. As intended, this means the files may change over time. If you require a point-in-time version, consider using a specific release tag in the URL.

- **Terraform Resource Modules**: `https://raw.githubusercontent.com/Azure/Azure-Verified-Modules/refs/heads/main/docs/static/module-indexes/TerraformResourceModules.csv`
- **Terraform Pattern Modules**: `https://raw.githubusercontent.com/Azure/Azure-Verified-Modules/refs/heads/main/docs/static/module-indexes/TerraformPatternModules.csv`
- **Terraform Utility Modules**: `https://raw.githubusercontent.com/Azure/Azure-Verified-Modules/refs/heads/main/docs/static/module-indexes/TerraformUtilityModules.csv`

#### Finding AVM Modules

1. Use `microsoft-learn` MCP server to search for modules
2. Check [Terraform Registry for Azure Verified Modules](https://registry.terraform.io/namespaces/Azure)
3. Module naming pattern: `Azure/avm-{type}-{service}-{resource}/azurerm`

### Module Types and Naming Conventions

- **Resource Modules**: `Azure/avm-res-{service}-{resource}/azurerm`
  - Example: `Azure/avm-res-storage-storageaccount/azurerm`
- **Pattern Modules**: `Azure/avm-ptn-{pattern}/azurerm`
  - Example: `Azure/avm-ptn-aks-enterprise/azurerm`
- **Utility Modules**: `Azure/avm-utl-{utility}/azurerm`
  - Example: `Azure/avm-utl-regions/azurerm`

**Service Naming Rules:**
- Use kebab-case for services and resources
- Follow Azure service names (e.g., `storage-storageaccount`, `network-virtualnetwork`)

### Using AVM from Examples

1. Copy the example code from the module documentation
2. Replace `source = "../../"` with `source = "Azure/avm-res-{service}-{resource}/azurerm"`
3. Add `version = "~> 1.0"` (use latest available)
4. Set `enable_telemetry = true`

### AVM Module Examples

```hcl
# Resource Group with AVM
module "resource_group" {
  source  = "Azure/avm-res-resources-resourcegroup/azurerm"
  version = "~> 0.1"

  enable_telemetry = true
  location         = var.location
  name             = var.resource_group_name
}

# Key Vault with AVM
module "keyvault" {
  source  = "Azure/avm-res-keyvault-vault/azurerm"
  version = "~> 0.5"

  enable_telemetry           = true
  name                       = var.keyvault_name
  resource_group_name        = azurerm_resource_group.main.name
  location                   = azurerm_resource_group.main.location
  tenant_id                  = data.azurerm_client_config.current.tenant_id
  enable_rbac_authorization  = true
}

# Virtual Network with AVM
module "vnet" {
  source  = "Azure/avm-res-network-virtualnetwork/azurerm"
  version = "~> 0.1"

  enable_telemetry    = true
  name                = var.vnet_name
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  address_space       = ["10.0.0.0/16"]
}
```

### Version Management

#### Check Available Versions

- Endpoint: `https://registry.terraform.io/v1/modules/Azure/{module}/azurerm/versions`
- Example: `https://registry.terraform.io/v1/modules/Azure/avm-res-storage-storageaccount/azurerm/versions`

#### Version Pinning Best Practices

- Use pessimistic version constraints: `version = "~> 1.0"`
- Pin to specific versions for production: `version = "1.2.3"`
- Always review changelog before upgrading

### Module Sources

#### Terraform Registry

- **URL Pattern**: `https://registry.terraform.io/modules/Azure/{module}/azurerm/latest`
- **Example**: `https://registry.terraform.io/modules/Azure/avm-res-storage-storageaccount/azurerm/latest`

#### GitHub Repository

- **URL Pattern**: `https://github.com/Azure/terraform-azurerm-avm-{type}-{service}-{resource}`
- **Examples**:
  - Resource: `https://github.com/Azure/terraform-azurerm-avm-res-storage-storageaccount`
  - Pattern: `https://github.com/Azure/terraform-azurerm-avm-ptn-aks-enterprise`

## When to Apply

This instruction applies when:
- Creating or modifying Terraform configurations
- Designing cloud infrastructure
- Implementing Infrastructure as Code (IaC)

## File Structure

```text
terraform/
  ├── main.tf              # Main resources
  ├── variables.tf         # Variable definitions
  ├── outputs.tf           # Output definitions
  ├── providers.tf         # Provider configuration
  ├── versions.tf          # Version constraints
  ├── locals.tf            # Local values
  ├── terraform.tfvars     # Variable values (gitignored)
  └── modules/             # Reusable modules
      └── storage/
          ├── main.tf
          ├── variables.tf
          └── outputs.tf
```

Align with the ISE Terraform structure guidelines: https://raw.githubusercontent.com/microsoft/code-with-engineering-playbook/main/docs/CI-CD/recipes/terraform/terraform-structure-guidelines.md

## Variables

### Define with Descriptions and Validation

```hcl
variable "environment" {
  description = "The deployment environment (dev, test, prod)"
  type        = string

  validation {
    condition     = contains(["dev", "test", "prod"], var.environment)
    error_message = "Environment must be dev, test, or prod."
  }
}

variable "location" {
  description = "Azure region for resources"
  type        = string
  default     = "eastus2"
}

variable "tags" {
  description = "Tags to apply to all resources"
  type        = map(string)
  default     = {}
}

variable "admin_password" {
  description = "Administrator password"
  type        = string
  sensitive   = true
}
```

## Providers

### Pin Provider Versions

```hcl
# versions.tf
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.80"
    }
  }
}

# providers.tf
provider "azurerm" {
  features {
    key_vault {
      purge_soft_delete_on_destroy = false
    }
  }
}
```

## Naming Conventions

Use consistent naming with locals:

```hcl
locals {
  name_prefix = "${var.project}-${var.environment}"

  common_tags = merge(var.tags, {
    Environment = var.environment
    Project     = var.project
    ManagedBy   = "Terraform"
  })
}

resource "azurerm_resource_group" "main" {
  name     = "${local.name_prefix}-rg"
  location = var.location
  tags     = local.common_tags
}
```

## Modules

### Create Reusable Modules

```hcl
# modules/storage/main.tf
resource "azurerm_storage_account" "this" {
  name                     = var.name
  resource_group_name      = var.resource_group_name
  location                 = var.location
  account_tier             = var.account_tier
  account_replication_type = var.replication_type
  min_tls_version          = "TLS1_2"

  blob_properties {
    versioning_enabled = true
  }

  tags = var.tags
}

# modules/storage/variables.tf
variable "name" {
  description = "Storage account name"
  type        = string
}

variable "resource_group_name" {
  description = "Resource group name"
  type        = string
}

variable "location" {
  description = "Azure region"
  type        = string
}

variable "account_tier" {
  description = "Storage account tier"
  type        = string
  default     = "Standard"
}

variable "replication_type" {
  description = "Storage replication type"
  type        = string
  default     = "LRS"
}

variable "tags" {
  description = "Tags to apply"
  type        = map(string)
  default     = {}
}

# modules/storage/outputs.tf
output "id" {
  description = "Storage account ID"
  value       = azurerm_storage_account.this.id
}

output "primary_blob_endpoint" {
  description = "Primary blob endpoint"
  value       = azurerm_storage_account.this.primary_blob_endpoint
}
```

### Use Modules

```hcl
module "storage" {
  source = "./modules/storage"

  name                = "${local.name_prefix}st"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  tags                = local.common_tags
}
```

## State Management

### Remote State Configuration

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-state-rg"
    storage_account_name = "tfstateaccount"
    container_name       = "tfstate"
    key                  = "project.terraform.tfstate"
  }
}
```

- Ensure the backend storage account uses encryption at rest (Azure Storage defaults) and strict access control (RBAC; private endpoints where appropriate).
- Separate state per environment **and** per critical boundary (e.g., landing zones vs app stacks); avoid shared state across unrelated domains.

## Workspace & Environment Isolation

- Use one workspace per environment boundary (e.g., `dev-api`, `staging-dataops`, `prod-ml`).
- Always select the workspace explicitly in CLI/CI (`terraform workspace select <env>`).
- Document rollback and drift recovery steps per workspace.
- Never mix environments in a single workspace/state file; document workspace naming conventions (e.g., `prod-core`, `nonprod-data`).

## Diagnostics Tiering

- Define **Baseline** vs **Investigation** tiers explicitly (Baseline = default-on for required signals; Investigation = time-boxed, opt-in) and align with AVM solution-development guidance.
- Never enable all diagnostic categories globally.
- Scope diagnostic categories per resource type and environment to control cost and noise.

## Outputs

```hcl
output "resource_group_name" {
  description = "The name of the resource group"
  value       = azurerm_resource_group.main.name
}

output "storage_account_id" {
  description = "The ID of the storage account"
  value       = module.storage.id
}

output "admin_password" {
  description = "The admin password"
  value       = var.admin_password
  sensitive   = true
}
```

## Conditional Resources

```hcl
variable "enable_monitoring" {
  description = "Enable monitoring resources"
  type        = bool
  default     = true
}

resource "azurerm_log_analytics_workspace" "main" {
  count = var.enable_monitoring ? 1 : 0

  name                = "${local.name_prefix}-logs"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
  tags                = local.common_tags
}
```

## Loops

```hcl
variable "storage_accounts" {
  description = "Storage accounts to create"
  type = map(object({
    tier             = string
    replication_type = string
  }))
  default = {}
}

resource "azurerm_storage_account" "accounts" {
  for_each = var.storage_accounts

  name                     = "${local.name_prefix}${each.key}"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = each.value.tier
  account_replication_type = each.value.replication_type
  tags                     = local.common_tags
}
```

## Testing

Use **stack-level tests** (root modules) with Terratest or `terraform test`:

```go
func TestTerraformStorage(t *testing.T) {
    terraformOptions := &terraform.Options{
        TerraformDir: "../terraform",
        Vars: map[string]interface{}{
            "environment": "test",
        },
    }

    defer terraform.Destroy(t, terraformOptions)
    terraform.InitAndApply(t, terraformOptions)

    storageId := terraform.Output(t, terraformOptions, "storage_account_id")
    assert.NotEmpty(t, storageId)
}
```

For **module-level tests** in AVM repositories, run `./avm unit-test` (terraform test) as part of contributor validation.

## Best Practices

### Platform Ownership & Governance

- Define clear ownership boundaries per platform layer (landing zones, shared services, app teams). Each layer owns its state, modules, and change approvals.
- Use a **blueprint repository** as the source of truth for baseline platform stacks. Sync changes into environment repos via **automated PRs** with review gates.
- Implement drift governance: schedule drift detection, alert on unauthorized changes, and require remediation PRs (no manual fixes outside Terraform).

### Declarative Maps & Lookup Patterns

- Prefer **smart lookup maps** in `locals` to avoid deeply nested conditionals (e.g., region SKU, environment sizing, naming rules).
- Model access as **declarative permission maps** (role → principals) and generate assignments from data, not ad-hoc resources.

### Versioning & Pipeline Workflow

- Define a **versioning policy** for modules and stacks (SemVer for shared modules; environment stacks pinned to exact versions).
- Use a **pipeline workflow** that enforces: format → validate → plan → policy checks (OPA/Conftest or equivalent) → apply with manual approval for prod.
- Require **upgrade PRs** for module/provider changes; include changelog links and impact summary.

### Onboarding, tfvars Validation, & Catalog Index

- Validate onboarding inputs (`.tfvars`) with schema checks and pre-commit validation (required keys, allowed values, naming rules).
- Maintain a **catalog index** that maps environments, subscriptions, regions, and owners to their Terraform stacks for discoverability.

### AVM Module Usage

- ✅ **Always** pin module and provider versions
- ✅ **Start** with official examples from module documentation
- ✅ **Review** all inputs and outputs before implementation
- ✅ **Enable** telemetry: `enable_telemetry = true`
- ✅ **Opt-out only when required**: set `enable_telemetry = false` after privacy/compliance review and document the rationale
- ✅ **Use** AVM utility modules for common patterns
- ✅ **Follow** AzureRM provider requirements and constraints
- ✅ **Prefer AVM** when available; only build custom modules when no AVM module exists or when wrapping AVM in a composite pattern module
- ✅ **Compose thin wrappers** around AVM modules to encode platform standards (avoid recreating resource configuration)
- ✅ **Use a private module registry** (Terraform Cloud/Enterprise or internal registry) for internal wrapper modules

### Code Quality

- ✅ **Always** run `terraform fmt` after making changes
- ✅ **Always** run `terraform validate` after making changes
- ✅ **Use** meaningful variable names and descriptions
- ✅ **Add** proper tags and metadata
- ✅ **Document** complex configurations

### General Best Practices

1. **Pin provider and module versions**
2. **Use remote state** with locking
3. **Organize with modules** for reusability
4. **Use variables** for all configurable values
5. **Mark sensitive values** with `sensitive = true`
6. **Add descriptions** to all variables and outputs
7. **Use consistent naming** with locals
8. **Apply tags** to all resources
9. **Format code** with `terraform fmt`
10. **Validate** with `terraform validate`

### Validation Requirements

Before creating or updating any pull request:

```bash
# Format code
terraform fmt -recursive

# Validate syntax
terraform validate

# AVM-specific validation (MANDATORY for AVM repositories)
./avm pre-commit
./avm tflint
./avm pr-check
```

## Tool Integration

### Use Available Tools

- **Deployment Guidance**: Use `azure_get_deployment_best_practices` tool (if available)
- **Service Documentation**: Use `microsoft-learn` MCP server for Azure service-specific guidance
- **Schema Information**: Use `azure_get_schema_for_Bicep` for Bicep resources (if available)
- **AVM Versioning**: Verify AVM module versions and deprecation notes in the AVM docs or Terraform Registry before scaffolding

### GitHub Copilot Integration

When working with AVM repositories:

1. Always check for existing modules before creating new resources
2. Use the official examples as starting points
3. Run all validation tests before committing
4. Document any customizations or deviations from examples

## Troubleshooting

### Common Issues

1. **Version Conflicts**: Always check compatibility between module and provider versions
2. **Missing Dependencies**: Ensure all required resources are created first
3. **Validation Failures**: Run AVM validation tools before committing
4. **Documentation**: Always refer to the latest module documentation

### Support Resources

- **AVM Documentation**: `https://azure.github.io/Azure-Verified-Modules/`
- **AVM Solution Development**: `https://azure.github.io/Azure-Verified-Modules/avm/solutions/terraform/`
- **GitHub Issues**: Report issues in the specific module's GitHub repository
- **Community**: Azure Terraform Provider GitHub discussions

## Compliance Checklist

Before submitting any AVM-related code:

- [ ] Module version is pinned
- [ ] Telemetry is enabled (`enable_telemetry = true`)
- [ ] Code is formatted (`terraform fmt`)
- [ ] Code is validated (`terraform validate`)
- [ ] AVM pre-commit checks pass (`./avm pre-commit`)
- [ ] TFLint checks pass (`./avm tflint`)
- [ ] AVM PR checks pass (`./avm pr-check`)
- [ ] Documentation is updated
- [ ] Examples are tested and working

## References

- Use `microsoft-learn` MCP server for latest Azure and Terraform documentation
- Use `iseplaybook` MCP server for ISE best practices
- [Azure Verified Modules Documentation](https://azure.github.io/Azure-Verified-Modules/)
- [Azure Verified Modules (Terraform Registry)](https://registry.terraform.io/namespaces/Azure)
- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [Azure Provider Documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
