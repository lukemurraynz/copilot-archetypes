---
description: 'Azure Verified Modules (AVM) and Bicep'
applyTo: '**/*.bicep, **/*.bicepparam'
---

# Azure Verified Modules (AVM) Bicep

## Overview

Azure Verified Modules (AVM) are pre-built, tested, and validated Bicep modules that follow Azure best practices. Use these modules to create, update, or review Azure Infrastructure as Code (IaC) with confidence.

## Discover Modules

### Bicep Public Registry

- Search for modules: `br/public:avm/res/{service}/{resource}:{version}`
- Browse available modules: `https://github.com/Azure/bicep-registry-modules/tree/main/avm/res`
- Example: `br/public:avm/res/storage/storage-account:0.30.0`

### Official AVM Index

- **Bicep Resource Modules**: `https://raw.githubusercontent.com/Azure/Azure-Verified-Modules/refs/heads/main/docs/static/module-indexes/BicepResourceModules.csv`
- **Bicep Pattern Modules**: `https://raw.githubusercontent.com/Azure/Azure-Verified-Modules/refs/heads/main/docs/static/module-indexes/BicepPatternModules.csv`

### Module Documentation

- **GitHub Repository**: `https://github.com/Azure/bicep-registry-modules/tree/main/avm/res/{service}/{resource}`
- **README**: Each module contains comprehensive documentation with examples

### Resource vs Pattern Modules

- Prefer **resource modules** for composing custom architectures.
- Use **pattern modules** for common end-to-end scenarios.

## Use Modules

### From Examples

1. Review module README in `https://github.com/Azure/bicep-registry-modules/tree/main/avm/res/{service}/{resource}`
2. Copy example code from module documentation
3. Verify the README badge/module index for the latest stable version (examples can lag)
4. Reference module using `br/public:avm/res/{service}/{resource}:{version}`
5. Configure required and optional parameters

### Example Usage

```bicep
module storageAccount 'br/public:avm/res/storage/storage-account:0.30.0' = {
  name: 'storage-account-deployment'
  scope: resourceGroup()
  params: {
    name: storageAccountName
    location: location
    skuName: 'Standard_LRS'
    tags: tags
  }
}
```

### When AVM Module Not Available

If no AVM module exists for a resource type:

- Check the AVM Bicep Resource Index to confirm the module does not exist.
- Use native Bicep resource declarations with the latest stable API version.
- Consider internal, AVM-inspired modules for repeated patterns so you can migrate later with minimal change.

## Author Code

### Module References

- **Resource Modules**: `br/public:avm/res/{service}/{resource}:{version}`
- **Pattern Modules**: `br/public:avm/ptn/{pattern}:{version}`
- Example: `br/public:avm/res/network/virtual-network:0.7.2`

### Symbolic Names

- Use lowerCamelCase for all names (variables, parameters, resources, modules)
- Use resource type descriptive names (e.g., `storageAccount` not `storageAccountName`)
- Avoid 'name' suffix in symbolic names as they represent the resource, not the resource's name
- Avoid distinguishing variables and parameters by suffixes

```bicep
module storageAccount 'br/public:avm/res/storage/storage-account:0.30.0' = {
  name: 'storage-account-deployment'
  params: {
    name: storageAccountName
  }
}

resource storageAccountFirewall 'Microsoft.Storage/storageAccounts/networkRuleSets@2023-05-01' = {
  name: '${storageAccountName}/default'
  properties: {
    defaultAction: 'Deny'
  }
}
```

### Module Versioning

- Always pin to specific module versions: `:{version}`
- Use semantic versioning (e.g., `:0.30.0`)
- Review module changelog before upgrading
- Test version upgrades in non-production environments first

### Code Structure

- ✅ **Declare** parameters at top of file with `@description()` decorators
- ✅ **Use** `@sys.description()` only when a parameter named `description` causes a naming collision
- ✅ **Specify** `@minLength()` and `@maxLength()` for naming parameters
- ✅ **Use** `@allowed()` decorator sparingly to avoid blocking valid deployments
- ✅ **Use** union types for environment selectors (e.g., `'dev' | 'staging' | 'prod'`) when supported
- ✅ **Set** default values safe for test environments (low-cost SKUs)
- ✅ **Use** variables for complex expressions instead of embedding in resource properties
- ✅ **Leverage** `loadJsonContent()` for external configuration files

### Resource References

- ✅ **Use** symbolic names for references (e.g., `storageAccount.id`) not `reference()` or `resourceId()`
- ✅ **Create** dependencies through symbolic names, not explicit `dependsOn`
- ✅ **Use** `existing` keyword for accessing properties from other resources
- ✅ **Access** module outputs via dot notation (e.g., `storageAccount.outputs.resourceId`)

### Resource Naming

- ✅ **Use** `uniqueString()` with meaningful prefixes for unique names
- ✅ **Add** prefixes since some resources don't allow names starting with numbers
- ✅ **Respect** resource-specific naming constraints (length, characters)

### Child Resources

- ✅ **Avoid** excessive nesting of child resources
- ✅ **Use** `parent` property or nesting instead of constructing names manually

### Security

- ❌ **Never** include secrets or keys in outputs
- ✅ **Use** resource properties directly in outputs (e.g., `storageAccount.outputs.primaryBlobEndpoint`)
- ✅ **Prefer** Key Vault or Managed Identity over passing keys as parameters
- ✅ **Mask** secrets in parameter files and CI variables
- ✅ **Enable** managed identities where possible
- ✅ **Tier diagnostics**: Baseline (minimal required categories) vs Investigation (time-boxed deep dive) — never enable all categories by default
- ✅ **Disable** public access when network isolation is enabled

### Types

- ✅ **Import** types from modules when available: `import { deploymentType } from './module.bicep'`
- ✅ **Use** user-defined types for complex parameter structures
- ✅ **Leverage** type inference for variables

```bicep
import { storageAccountParams } from './types.bicep'

type alertRule = {
  name: string
  severity: 'Sev0' | 'Sev1' | 'Sev2'
  enabled: bool
}

param rules array<alertRule>
param storage storageAccountParams
```

### Documentation

- ✅ **Include** helpful `//` comments for complex logic
- ✅ **Use** `@description()` on all parameters with clear explanations
- ✅ **Document** non-obvious design decisions

## Validate & Integrate

### Build Validation (MANDATORY)

After any changes to Bicep files, run the following commands to ensure all files build successfully:

```shell
# Ensure Bicep CLI is up to date
az bicep upgrade

# Build and validate all changed Bicep files (not just main.bicep)
az bicep build --file main.bicep
```

### Bicep Parameter Files

- ✅ **Always** update accompanying `*.bicepparam` files when modifying `*.bicep` files
- ✅ **Validate** parameter files match current parameter definitions
- ✅ **Test** deployments with parameter files before committing

### What-If Validation

- Run `az deployment what-if` at the appropriate scope (subscription or resource group) as part of pre-merge validation.

## Tool Integration

### Use Available Tools

- **Schema Information**: Use resource schema lookup tools when available
- **Deployment Guidance**: Use official Azure/Bicep documentation for service-specific guidance

### GitHub Copilot Integration

When working with Bicep:

1. Prompt: "Use AVM module `br/public:avm/res/{service}/{resource}:{version}` where available; otherwise use native Bicep with latest stable API."
2. Prompt: "Cross-check README badge/module index before pinning versions (examples can lag)."
3. Prompt: "Generate validation commands (az bicep build + az deployment what-if at correct scope)."
4. Update accompanying `.bicepparam` files.
5. Document customizations or deviations from examples.

## Troubleshooting

### Common Issues

1. **Module Version**: Always specify exact version in module reference
2. **Missing Dependencies**: Ensure resources are created before dependent modules
3. **Validation Failures**: Run `az bicep build` to identify syntax/type errors
4. **Parameter Files**: Ensure `.bicepparam` files are updated when parameters change

### Support Resources

- **AVM Documentation**: `https://azure.github.io/Azure-Verified-Modules/`
- **Bicep Registry**: `https://github.com/Azure/bicep-registry-modules`
- **Bicep Documentation**: `https://learn.microsoft.com/azure/azure-resource-manager/bicep/`
- **Best Practices**: `https://learn.microsoft.com/azure/azure-resource-manager/bicep/best-practices`

## Validation & Compliance

### Compliance Checklist

Before submitting any Bicep code:

- [ ] Code builds successfully (`az bicep build`)
- [ ] `az deployment what-if` run at the correct scope
- [ ] Accompanying `.bicepparam` files updated
- [ ] Module versions pinned
- [ ] No secrets in outputs
