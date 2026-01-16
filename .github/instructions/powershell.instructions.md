---
applyTo: "**/*.ps1,**/*.psm1"
description: 'PowerShell cmdlet and scripting best practices based on Microsoft guidelines'
---

# PowerShell Cmdlet Development Guidelines

This guide provides PowerShell-specific instructions to help GitHub Copilot generate idiomatic, safe, and maintainable scripts. It aligns with Microsoft's PowerShell cmdlet development guidelines.

**IMPORTANT**: Use the `iseplaybook` MCP server for automation best practices and the `microsoft-learn` MCP server for Azure/PowerShell guidance when version- or platform-dependent behavior is involved.

## Safe Automation Defaults

- Support **dry-run** mode and require an explicit `-Apply`/`-Write`/`-Update` switch for mutations.
- Calculate and display changes before executing them.
- Avoid prompts for secrets; accept via environment or managed identity.

## Naming Conventions

### Cmdlet Names

- **Verb-Noun Format:**
  - Use approved PowerShell verbs (check with `Get-Verb`)
  - Use singular nouns
  - PascalCase for both verb and noun
  - Avoid special characters and spaces

### Parameter Names

- Use PascalCase
- Choose clear, descriptive names
- Use singular form unless always multiple
- Follow PowerShell standard names (e.g., `Path`, `Name`, `Force`)

### Variable Names

- Use PascalCase for public variables
- Use camelCase for private variables
- Avoid abbreviations
- Use meaningful names

### Alias Avoidance

- Use full cmdlet names in scripts
- Avoid aliases (e.g., use `Get-ChildItem` instead of `gci`, `dir`, or `ls`)
- Use `Where-Object` instead of `?` or `where`
- Use `ForEach-Object` instead of `%`
- Document any custom aliases
- Use full parameter names

**Note:** Aliases are acceptable for interactive shell use but should be avoided in production scripts.

### Naming Example

```powershell
function Get-UserProfile {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory)]
        [string]$Username,

        [Parameter()]
        [ValidateSet('Basic', 'Detailed')]
        [string]$ProfileType = 'Basic'
    )

    process {
        # Logic here
    }
}
```

## Parameter Design

### Standard Parameters

- Use common parameter names (`Path`, `Name`, `Force`, `Verbose`, `WhatIf`)
- Follow built-in cmdlet conventions
- Use parameter aliases sparingly for specialized terms
- Document parameter purpose clearly

### Parameter Names and Types

- Use singular form unless always multiple
- Choose clear, descriptive names
- Follow PowerShell conventions
- Use PascalCase formatting
- Use common .NET types
- Implement proper validation attributes

### Validation

- Use `ValidateSet` for limited options
- Use `ValidateNotNullOrEmpty()` for required strings
- Use `ValidateRange()` for numeric constraints
- Use `ValidateScript()` for custom validation
- Enable tab completion where possible

### Switch Parameters

- Use `[switch]` for boolean flags
- Avoid `$true`/`$false` parameters
- Default to `$false` when omitted
- Use clear action names (e.g., `-Force`, `-PassThru`, `-Recurse`)

### Parameter Design Example

```powershell
function Set-ResourceConfiguration {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [Parameter()]
        [ValidateSet('Dev', 'Test', 'Prod')]
        [string]$Environment = 'Dev',

        [Parameter()]
        [switch]$Force,

        [Parameter()]
        [ValidateNotNullOrEmpty()]
        [string[]]$Tags
    )

    process {
        # Logic here
    }
}
```

## Pipeline and Output

### Pipeline Input

- Use `ValueFromPipeline` for direct object input
- Use `ValueFromPipelineByPropertyName` for property mapping
- Implement `Begin`/`Process`/`End` blocks for pipeline handling
- Document pipeline input requirements in help

### Output Objects

- Return rich objects, not formatted text
- Use `PSCustomObject` for structured data
- Avoid `Write-Host` for data output
- Enable downstream cmdlet processing
- Use `Write-Output` explicitly when needed
- Emit structured objects suitable for CSV/JSON export; avoid `Write-Host` as the only output

### Pipeline Streaming

- Output one object at a time in the `process` block
- Use `process` block for streaming operations
- Avoid collecting large arrays in memory
- Enable immediate downstream processing

### PassThru Pattern

- Default to no output for action cmdlets (Set-, New-, Remove-)
- Implement `-PassThru` switch for object return
- Return modified/created object when `-PassThru` is specified
- Use `Write-Verbose` or `Write-Warning` for status updates

### Pipeline Example

```powershell
function Update-ResourceStatus {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory, ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [string]$Name,

        [Parameter(Mandatory)]
        [ValidateSet('Active', 'Inactive', 'Maintenance')]
        [string]$Status,

        [Parameter()]
        [switch]$PassThru
    )

    begin {
        Write-Verbose 'Starting resource status update process'
        $timestamp = Get-Date
    }

    process {
        # Process each resource individually
        Write-Verbose "Processing resource: $Name"

        $resource = [PSCustomObject]@{
            Name        = $Name
            Status      = $Status
            LastUpdated = $timestamp
            UpdatedBy   = $env:USERNAME
        }

        # Only output if PassThru is specified
        if ($PassThru.IsPresent) {
            Write-Output $resource
        }
    }

    end {
        Write-Verbose 'Resource status update process completed'
    }
}
```


## Script Structure

### Comment-Based Help

Include comment-based help for all public-facing functions and cmdlets:

```powershell
<#
.SYNOPSIS
    Brief description of what the script does.

.DESCRIPTION
    Detailed description of the script's purpose, behavior, and usage.

.PARAMETER Name
    Description of the Name parameter.

.PARAMETER Force
    Skip confirmation prompts.

.EXAMPLE
    .\Script.ps1 -Name "Example"
    Runs the script with the specified name.

.EXAMPLE
    .\Script.ps1 -Name "Example" -Force
    Runs the script without confirmation prompts.

.OUTPUTS
    System.Management.Automation.PSCustomObject
    Returns an object with resource status information.

.NOTES
    Author: Your Name
    Version: 1.0.0
    Requires: PowerShell 5.1 or later
#>
```

### Parameter Definition

Use `[CmdletBinding()]` and proper parameter attributes:

```powershell
function Get-ResourceData {
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [Parameter(Mandatory = $true, Position = 0, ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [Parameter(Mandatory = $false)]
        [ValidateRange(1, 100)]
        [int]$Count = 10,

        [Parameter()]
        [switch]$Force
    )

    begin {
        Write-Verbose "Starting resource data retrieval"
    }

    process {
        # Process each item from pipeline
    }

    end {
        Write-Verbose "Completed resource data retrieval"
    }
}
```

## Error Handling and Safety

### ShouldProcess Implementation

- Use `[CmdletBinding(SupportsShouldProcess = $true)]` for cmdlets that make changes
- Set appropriate `ConfirmImpact` level (Low, Medium, High)
- Call `$PSCmdlet.ShouldProcess()` before system changes
- Use `ShouldContinue()` for additional confirmations when needed
- Respect `-WhatIf` and `-Confirm` parameters

### Message Streams

- `Write-Verbose` for operational details (shown with `-Verbose`)
- `Write-Warning` for warning conditions
- `Write-Error` for non-terminating errors
- `throw` or `$PSCmdlet.ThrowTerminatingError()` for terminating errors
- Avoid `Write-Host` except for user interface text
- `Write-Debug` for debugging information (shown with `-Debug`)

### Error Handling Pattern

- Use `try`/`catch` blocks for error management
- Set appropriate `ErrorAction` preferences
- Return meaningful error messages with context
- Use `ErrorVariable` when needed
- Include proper terminating vs non-terminating error handling
- In advanced functions with `[CmdletBinding()]`, prefer `$PSCmdlet.WriteError()` over `Write-Error`
- In advanced functions with `[CmdletBinding()]`, prefer `$PSCmdlet.ThrowTerminatingError()` over `throw`
- Construct proper `ErrorRecord` objects with category, target, and exception details

### Non-Interactive Design

- Accept input via parameters
- Avoid `Read-Host` in scripts
- Support automation scenarios
- Document all required inputs
- Use `-Force` switch to bypass confirmations in automation

### Fail Fast

Set strict error handling at the start:

```powershell
$ErrorActionPreference = 'Stop'
Set-StrictMode -Version Latest
```

### Error Handling Example

```powershell
function Remove-UserAccount {
    [CmdletBinding(SupportsShouldProcess = $true, ConfirmImpact = 'High')]
    param(
        [Parameter(Mandatory, ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [string]$Username,

        [Parameter()]
        [switch]$Force
    )

    begin {
        Write-Verbose 'Starting user account removal process'
        $ErrorActionPreference = 'Stop'
    }

    process {
        try {
            # Validation
            if (-not (Test-UserExists -Username $Username)) {
                $errorRecord = [System.Management.Automation.ErrorRecord]::new(
                    [System.Exception]::new("User account '$Username' not found"),
                    'UserNotFound',
                    [System.Management.Automation.ErrorCategory]::ObjectNotFound,
                    $Username
                )
                $PSCmdlet.WriteError($errorRecord)
                return
            }

            # Confirmation
            $shouldProcessMessage = "Remove user account '$Username'"
            if ($Force -or $PSCmdlet.ShouldProcess($Username, $shouldProcessMessage)) {
                Write-Verbose "Removing user account: $Username"

                # Main operation
                Remove-ADUser -Identity $Username -ErrorAction Stop
                Write-Warning "User account '$Username' has been removed"
            }
        } catch [Microsoft.ActiveDirectory.Management.ADException] {
            $errorRecord = [System.Management.Automation.ErrorRecord]::new(
                $_.Exception,
                'ActiveDirectoryError',
                [System.Management.Automation.ErrorCategory]::NotSpecified,
                $Username
            )
            $PSCmdlet.ThrowTerminatingError($errorRecord)
        } catch {
            $errorRecord = [System.Management.Automation.ErrorRecord]::new(
                $_.Exception,
                'UnexpectedError',
                [System.Management.Automation.ErrorCategory]::NotSpecified,
                $Username
            )
            $PSCmdlet.ThrowTerminatingError($errorRecord)
        }
    }

    end {
        Write-Verbose 'User account removal process completed'
    }
}
```

### Simple Try/Catch Example

```powershell
try {
    $result = Invoke-RestMethod -Uri $uri -Method Get -ErrorAction Stop
    Write-Verbose "Request successful: $($result.message)"
} catch {
    Write-Error "Failed to make request: $_"
    throw
} finally {
    # Cleanup code
}
```

## Output and Logging

### Use Appropriate Output Cmdlets

```powershell
# Return data objects (preferred)
Write-Output $resultObject

# User-facing status messages (use sparingly)
Write-Host "Processing item: $name" -ForegroundColor Green

# Detailed debugging information
Write-Verbose "Connecting to $endpoint"

# Debug information
Write-Debug "Variable value: $debugInfo"

# Warnings
Write-Warning "Configuration file not found, using defaults"

# Non-terminating errors
Write-Error "Operation failed: $reason"

# Progress for long operations
Write-Progress -Activity "Processing" -Status "Item $i of $total" -PercentComplete (($i / $total) * 100)
```

### Timestamps for Long Operations

```powershell
function Write-Log {
    param([string]$Message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Write-Verbose "[$timestamp] $Message"
}
```

## Azure CLI Integration

### Check Prerequisites

```powershell
# Check for Azure CLI
if (-not (Get-Command az -ErrorAction SilentlyContinue)) {
    Write-Error "Azure CLI is not installed. Please install it from https://aka.ms/install-azure-cli"
    exit 1
}

# Verify login
$account = az account show --output json 2>$null | ConvertFrom-Json
if (-not $account) {
    Write-Error "Not logged in to Azure. Run 'az login' first."
    exit 1
}

Write-Host "Using subscription: $($account.name)"
```

### Parse JSON Output

```powershell
$resources = az resource list --output json | ConvertFrom-Json

if ($LASTEXITCODE -ne 0) {
    Write-Error "Azure CLI command failed"
    exit 1
}

foreach ($resource in $resources) {
    Write-Host "Resource: $($resource.name) ($($resource.type))"
}
```

## File and Path Handling

### Cross-Platform Paths

```powershell
# Use Join-Path for path construction
$configPath = Join-Path -Path $PSScriptRoot -ChildPath "config" | Join-Path -ChildPath "settings.json"

# Use $PSScriptRoot for script-relative paths
$scriptDir = $PSScriptRoot
```

### Check File Existence

```powershell
if (-not (Test-Path -Path $filePath -PathType Leaf)) {
    Write-Error "File does not exist: $filePath"
    exit 1
}
```

## Functions

### Define Reusable Functions

```powershell
function Get-ConfigValue {
    <#
    .SYNOPSIS
        Gets a configuration value from environment or file.
    #>
    [CmdletBinding()]
    param(
        [Parameter(Mandatory)]
        [string]$Name,

        [Parameter()]
        [string]$Default = ""
    )

    $envValue = [Environment]::GetEnvironmentVariable($Name)
    if ($envValue) {
        return $envValue
    }

    return $Default
}
```

## azd Integration

### Get Environment Values

```powershell
$envName = azd env get-values --output json | ConvertFrom-Json
if ($LASTEXITCODE -ne 0) {
    Write-Error "Failed to get azd environment values"
    exit 1
}

$apiEndpoint = $envName.API_ENDPOINT
```

## Documentation and Style

### Comment-Based Help Requirements

Include comment-based help for any public-facing function or cmdlet. Inside the function, add a `<# ... #>` help comment with at least:

- `.SYNOPSIS` Brief description
- `.DESCRIPTION` Detailed explanation
- `.PARAMETER` descriptions for each parameter
- `.EXAMPLE` sections with practical usage
- `.OUTPUTS` Type of output returned
- `.NOTES` Additional information (author, version, requirements)

### Consistent Formatting

- Follow consistent PowerShell style
- Use proper indentation (4 spaces recommended)
- Opening braces on same line as statement
- Closing braces on new line aligned with statement
- Use line breaks after pipeline operators for readability
- PascalCase for function and parameter names
- Avoid unnecessary whitespace

### Pipeline Support

- Implement `Begin`/`Process`/`End` blocks for pipeline functions
- Use `ValueFromPipeline` where appropriate
- Support pipeline input by property name when logical
- Return proper objects, not formatted text

## Best Practices

1. **Idempotency**: Scripts should be safe to run multiple times without side effects
2. **Validation**: Validate inputs before processing using parameter validation attributes
3. **Cleanup**: Use `finally` blocks or trap statements for cleanup operations
4. **Exit Codes**: Return meaningful exit codes (0 for success, non-zero for failure)
5. **Confirmation**: Use `ShouldProcess` for destructive operations
6. **Error Handling**: Implement comprehensive error handling with proper error records
7. **Pipeline Support**: Design functions to work with the pipeline when appropriate
8. **Output Design**: Return objects, not formatted text; use `-PassThru` for action cmdlets
9. **Verbose Support**: Provide verbose output for operational details
10. **Non-Interactive**: Design for automation; avoid `Read-Host`

## Full Example: End-to-End Cmdlet Pattern

```powershell
function New-Resource {
    <#
    .SYNOPSIS
        Creates a new resource with specified configuration.

    .DESCRIPTION
        The New-Resource cmdlet creates a new resource in the specified environment.
        It supports pipeline input and implements proper confirmation for safe operation.

    .PARAMETER Name
        The name of the resource to create. This parameter is mandatory and accepts
        pipeline input.

    .PARAMETER Environment
        The target environment for the resource. Valid values are 'Development' and
        'Production'. Defaults to 'Development'.

    .EXAMPLE
        New-Resource -Name "MyResource" -Environment Production
        Creates a new resource named "MyResource" in the Production environment.

    .EXAMPLE
        "Resource1", "Resource2" | New-Resource -Environment Development
        Creates multiple resources from pipeline input.

    .OUTPUTS
        System.Management.Automation.PSCustomObject
        Returns an object containing the resource name, environment, and creation timestamp.

    .NOTES
        Author: Your Name
        Version: 1.0.0
        Requires: PowerShell 5.1 or later
    #>
    [CmdletBinding(SupportsShouldProcess = $true, ConfirmImpact = 'Medium')]
    param(
        [Parameter(Mandatory = $true,
            ValueFromPipeline = $true,
            ValueFromPipelineByPropertyName = $true)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [Parameter()]
        [ValidateSet('Development', 'Production')]
        [string]$Environment = 'Development'
    )

    begin {
        Write-Verbose 'Starting resource creation process'
        $ErrorActionPreference = 'Stop'
    }

    process {
        try {
            if ($PSCmdlet.ShouldProcess($Name, "Create new resource in $Environment")) {
                Write-Verbose "Creating resource: $Name in $Environment"

                # Resource creation logic here
                $resource = [PSCustomObject]@{
                    PSTypeName  = 'Custom.Resource'
                    Name        = $Name
                    Environment = $Environment
                    Created     = Get-Date
                    CreatedBy   = $env:USERNAME
                }

                Write-Verbose "Resource '$Name' created successfully"
                Write-Output $resource
            }
        } catch {
            $errorRecord = [System.Management.Automation.ErrorRecord]::new(
                $_.Exception,
                'ResourceCreationFailed',
                [System.Management.Automation.ErrorCategory]::NotSpecified,
                $Name
            )
            $PSCmdlet.ThrowTerminatingError($errorRecord)
        }
    }

    end {
        Write-Verbose 'Completed resource creation process'
    }
}
```

## Testing

### Pester Tests

```powershell
Describe "Get-ConfigValue" {
    It "Returns environment variable when set" {
        $env:TEST_VALUE = "expected"
        Get-ConfigValue -Name "TEST_VALUE" | Should -Be "expected"
    }

    It "Returns default when environment variable not set" {
        Remove-Item Env:TEST_VALUE -ErrorAction SilentlyContinue
        Get-ConfigValue -Name "TEST_VALUE" -Default "default" | Should -Be "default"
    }

    It "Accepts pipeline input" {
        "TestValue" | Get-ConfigValue -Default "default" | Should -Be "default"
    }

    It "Supports WhatIf" {
        { New-Resource -Name "Test" -WhatIf } | Should -Not -Throw
    }
}
```

## References

- [PowerShell Documentation](https://learn.microsoft.com/powershell/)
- [PowerShell Best Practices](https://poshcode.gitbook.io/powershell-practice-and-style/)
- [Azure PowerShell Documentation](https://learn.microsoft.com/powershell/azure/)
- [ISE Shell Checklist](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/bash/)
