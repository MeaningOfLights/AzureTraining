# Azure - Powershell

## Purpose

At the end of this module, you will:
* Learn how to install and use VSCode's Powershell Terminal
* Learn how to install Azure Powershell Modules
* Learn how to login to Azure using Powershell 
* Learn how to run Powershell commands to fetch Azure info

## What is Azure PowerShell?

Azure PowerShell is a set of cmdlets for managing Azure resources directly from the PowerShell command line on any platform.

### ![Activity][activity] 2.40.1 Setup VSCode, Powershell and login to Azure

1. Open Powershell or download and install VSCode if you don't have it already: https://code.visualstudio.com/download

1. Open Powershell or VSCode and start a new Terminal. Start > run > Powershell

![Powershell](../images/powershell-login.png)

2. Setup Azure Powershell if its not been installed.

```
Exit-PSSession
Uninstall-AzureRm
Install-Module Az -Scope CurrentUser -AllowClobber
Set-ExecutionPolicy Unrestricted -Scope CurrentUser
```

3. In the Terminal enter the commands, copy/pasting the commands from each line below into Powershell or VSCode's Terminal Window, pressing enter to execute each one individually

* PS C:\Users\p738753> Exit-PSSession
* PS C:\Users\p738753> Uninstall-AzureRm
* PS C:\Users\p738753> Install-Module Az -Scope CurrentUser -Allowclobber
Type A for [All]
* PS C:\Users\p738753> Set-ExecutionPolicy Unrestricted -Scope CurrentUser
* PS C:\Users\p738753> login-azaccount

------- ---------------- -------- -----------

Simple commands to fetch information from Azure (PLEASE TYPE THESE IN YOURSELF... DON'T COPY!):

* PS C:\Users\p738753> Get-AZVM -status

* PS C:\Users\p738753> Get-AzSubscription

* PS C:\Users\p738753> Get-AzResourceGroup 

* PS C:\Users\p738753> Get-AzStorageAccount 



[activity]: ../icons/activity.png "Workshop Activity!"
[discussion]: ../icons/discussion.png "Team Discussion!"
[reading]: ../icons/reading.png "Further Reading!"
