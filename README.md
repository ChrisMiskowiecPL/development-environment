# Development Environment Setup
1. Upgrade WinGet to latest, here: https://github.com/microsoft/winget-cli/releases
2. In a Windows PowerShell terminal running as Administrator:
    1. Install WinGet Client Package: `Install-Module Microsoft.WinGet.Client -Scope AllUsers -Force`
    2. Run DSC: `winget configure -f .\devenv.winget.yaml`
