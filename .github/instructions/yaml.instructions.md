---
applyTo: "**/azure-pipelines.yml, **/azure-pipelines*.yml, **/*.pipeline.yml,.github/workflows/**"
description: 'YAML and CI/CD pipeline best practices and guidelines'
---

# YAML and CI/CD Pipeline Instructions

Follow ISE CI/CD Best Practices for pipeline development.

**IMPORTANT**: Use the `iseplaybook` MCP server to get the latest CI/CD best practices. Use `microsoft-learn` MCP server for Azure Pipelines documentation. Do not assume—verify current guidance.

## When to Apply

This instruction applies when:
- Creating or modifying YAML configuration files
- Building CI/CD pipelines (GitHub Actions, Azure Pipelines)
- Configuring infrastructure definitions (Kubernetes, Docker Compose)

## YAML Formatting

### Basic Style

```yaml
# Use 2-space indentation
services:
  api:
    image: myapp:latest
    ports:
      - "8080:8080"

# Use proper quoting
message: "Hello, World"
version: "1.0.0"  # Quote version strings

# Use multiline strings appropriately
description: |
  This is a multiline
  description that preserves
  line breaks.

script: >
  This is a folded multiline
  string that becomes a single line.
```

## GitHub Actions

### Workflow Structure

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read
  pull-requests: write

env:
  NODE_VERSION: '20'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

### Security Best Practices

```yaml
# Pin action versions with SHA
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1

# Use minimal permissions
permissions:
  contents: read

# Use secrets for sensitive values
env:
  API_KEY: ${{ secrets.API_KEY }}

# Avoid shell injection
- run: echo "Processing ${{ github.event.inputs.name }}"  # Risky
- run: echo "Processing ${NAME}"  # Better
  env:
    NAME: ${{ github.event.inputs.name }}
```

### Reusable Workflows

```yaml
# .github/workflows/reusable-build.yml
name: Reusable Build Workflow

on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      token:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Building for ${{ inputs.environment }}"
```

### Matrix Builds

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [18, 20, 22]
      fail-fast: false

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

## Azure Pipelines

### Pipeline Structure

```yaml
trigger:
  branches:
    include:
      - main
  paths:
    exclude:
      - '**/*.md'

pool:
  vmImage: 'ubuntu-latest'

variables:
  - name: buildConfiguration
    value: 'Release'

stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - task: DotNetCoreCLI@2
            displayName: 'Restore packages'
            inputs:
              command: restore
              projects: '**/*.csproj'

          - task: DotNetCoreCLI@2
            displayName: 'Build solution'
            inputs:
              command: build
              projects: '**/*.csproj'
              arguments: '--configuration $(buildConfiguration)'

  - stage: Deploy
    dependsOn: Build
    condition: succeeded()
    jobs:
      - deployment: Deploy
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo 'Deploying...'
```

### Templates

```yaml
# templates/build-template.yml
parameters:
  - name: buildConfiguration
    type: string
    default: 'Release'

steps:
  - task: DotNetCoreCLI@2
    displayName: 'Build'
    inputs:
      command: build
      arguments: '--configuration ${{ parameters.buildConfiguration }}'

# azure-pipelines.yml
stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - template: templates/build-template.yml
            parameters:
              buildConfiguration: 'Release'
```

## Best Practices

### General YAML

1. **Use consistent indentation** (2 spaces)
2. **Quote strings** that could be misinterpreted (versions, special chars)
3. **Use anchors** for repeated content
4. **Validate YAML** before committing

### CI/CD Pipelines

1. **Pin action/task versions** for reproducibility
2. **Use caching** for dependencies
3. **Fail fast** by default, but configure matrix appropriately
4. **Use minimal permissions** (principle of least privilege)
5. **Separate build and deploy stages**
6. **Use environments** for deployment approvals
7. **Include status badges** in README
8. **Run security scans** (dependency scanning, SAST)

### Secrets Management

1. **Never hardcode secrets** in YAML files
2. **Use GitHub Secrets** or Azure Key Vault
3. **Mask secrets** in logs
4. **Rotate secrets** regularly
5. **Use OIDC** for cloud authentication when possible

## YAML Anchors and Aliases

```yaml
# Define anchor
defaults: &defaults
  timeout: 30
  retries: 3

# Use alias
service1:
  <<: *defaults
  name: api

service2:
  <<: *defaults
  name: web
  timeout: 60  # Override
```

## Common Patterns

### Conditional Steps

```yaml
# GitHub Actions
- name: Deploy
  if: github.ref == 'refs/heads/main'
  run: ./deploy.sh

# Azure Pipelines
- script: ./deploy.sh
  condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')
```

### Environment Variables

```yaml
# GitHub Actions
env:
  GLOBAL_VAR: 'value'

jobs:
  build:
    env:
      JOB_VAR: 'value'
    steps:
      - run: echo $GLOBAL_VAR $JOB_VAR
        env:
          STEP_VAR: 'value'
```

## References

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Azure Pipelines Documentation](https://learn.microsoft.com/azure/devops/pipelines/)
- [ISE CI/CD Best Practices](https://microsoft.github.io/code-with-engineering-playbook/CI-CD/)
- [YAML Specification](https://yaml.org/spec/)
