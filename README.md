# Development Environment Setup

A Windows Desired State Configuration (DSC) project using WinGet to automate the setup of a development environment.

## Prerequisites

1. **Windows 10/11** with WinGet installed
2. **Upgrade WinGet** to the latest version: https://github.com/microsoft/winget-cli/releases
3. **PowerShell 5.1+** (Windows PowerShell or PowerShell 7)

## Installation

Open **Windows PowerShell as Administrator** and run:

```powershell
# Install required PowerShell modules
Install-Module Microsoft.WinGet.Client -Scope AllUsers -Force
Install-Module Microsoft.WinGet.DSC -Scope AllUsers -Force
```

## Usage

Apply configurations in order from the `configs/` directory:

```powershell
# 1. Install development tools
winget configure -f .\configs\02-devtools.dsc.yaml

# 2. Install VS Code extensions
winget configure -f .\configs\03-vscode-extensions.dsc.yaml

# 3. Apply settings (Git config, fonts, terminal settings)
winget configure -f .\configs\04-settings.dsc.yaml
```

Or apply all configurations at once:

```powershell
Get-ChildItem .\configs\*.yaml | Sort-Object Name | ForEach-Object { winget configure -f $_.FullName }
```

## Adding New Items

All configuration files follow the [WinGet DSC schema v0.2](https://aka.ms/configuration-dsc-schema/0.2).

### Adding a WinGet Package

Add to `configs/02-devtools.dsc.yaml`:

```yaml
- resource: Microsoft.WinGet.DSC/WinGetPackage
  id: unique-id
  directives:
    description: Install Package Name
    securityContext: elevated  # optional, use if admin required
  settings:
    id: Publisher.PackageName  # find with: winget search <name>
    source: winget
```

### Adding a VS Code Extension

Add to `configs/03-vscode-extensions.dsc.yaml`:

```yaml
- resource: Microsoft.VSCode.Dsc/VSCodeExtension
  directives:
    description: Install Extension Name
    allowPrerelease: true
  settings:
    Name: publisher.extension-name  # find in VS Code marketplace URL
```

### Adding a Custom Script

Add to `configs/04-settings.dsc.yaml`:

```yaml
- resource: PSDesiredStateConfiguration/Script
  id: unique-id
  directives:
    description: What this script does
    securityContext: elevated  # optional, use if admin required
  settings:
    GetScript: |
      # Return current state as hashtable
      @{ Result = "current value" }
    TestScript: |
      # Return $true if desired state is already set, $false otherwise
      $true
    SetScript: |
      # Apply the desired state (only runs if TestScript returns $false)
```

### Re-running After Changes

DSC is idempotent - you can re-run any configuration file after adding new items. It will only apply changes for items not already in the desired state:

```powershell
winget configure -f .\configs\02-devtools.dsc.yaml
```
