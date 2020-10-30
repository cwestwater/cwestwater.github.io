---
layout: single
classes: wide
title: "PowerCLI with PowerShell 5.x and 7.x - Part 2"
date: 2020-10-30
category: [VMware]
excerpt: "How does updating the PowerCLI module work on a computer with both PowerShell 5.x and 7.x"
---

## Introduction

In [Part 1]({{ site.baseurl }}{% post_url 2020-10-19-PowerShell-PowerCLI-Part-1 %}) I looked at how the installation and use of the PowerCLI module worked when you had both PowerShell v5.1 and v7.x installed.

In this post I will look at what happens when trying to update from an older version of PowerCLI and what that means when using the two PowerShell versions.

### PowerShell and PowerCLI Versions

At the time of this post the two current versions of PowerShell are:

- 'Legacy' PowerShell v5.1
- PowerShell Core v7.0.3

I will be testing using two versions of PowerCLI

- v11.5.0 released in October 2019
- v12.1.0 released in October 2020

Using the older v11.5.0 as the initial installed version, then trying to upgrade to the current version v120.1.0.

All this will be done on a clean install of Windows 10 patched up to the time of this blog post.

### Forcing an Install of an Older Module

As PowerCLI is hosted on the [PowerShell Gallery](https://www.powershellgallery.com/packages/VMware.PowerCLI/) it is easy to see the various versions of PowerCLI that have been released:

![PowerCLI in PowerShell Gallery]({{ site.url }}/assets/images/PowerCLI-PowerShell-Gallery.png)

By default `Install-Module` will download the latest version of a module available in the PowerShell Gallery. If we want to force the installation of a specific version, add the switch `-RequiredVersion` to the `Install-Module` cmdlet:

```powershell
Install-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921
```

### Scenario 1

The scenario I am testing is one that may be pretty common. I have an older version of PowerCLI installed in v5.1:

```powershell
PS C:\Windows\system32> Install-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921

PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   11.5.0.... VMware.PowerCLI
```

As found in the [previous post]({{ site.baseurl }}{% post_url 2020-10-19-PowerShell-PowerCLI-Part-1 %}) by default PowerShell v5.1 installs modules to the `AllUsers` scope so the modules are located in `C:\Program Files\WindowsPowerShell\Modules` which is a module path in PowerShell v7. So if we check the availability of PowerCLI v11.5 in PowerShell v7:

```powershell
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   11.5.0.14…            VMware.PowerCLI                     Desk
```

So then PowerShell 7 comes along and you start using that. Next you think lets install PowerCLI:

```powershell
PS C:\Windows\System32> Install-Module -Name VMware.PowerCLI
WARNING: Version '11.5.0.14912921' of module 'VMware.PowerCLI' is already installed at 'C:\Program Files\WindowsPowerShell\Modules\VMware.PowerCLI\11.5.0.14912921'. To install version '12.1.0.17009493', run Install-Module and add the -Force parameter, this command will install version '12.1.0.17009493' side-by-side with version '11.5.0.14912921'.
```

In this case the 'fix' is to run `Update-Module -Name VMware.PowerCLI` in PowerShell v5.1:

```powershell
PS C:\Windows\system32> Update-Module -Name VMware.PowerCLI
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   12.1.0.... VMware.PowerCLI
Manifest   11.5.0.... VMware.PowerCLI
```

Then checking PowerShell v7:

```powershell
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   12.1.0.17…            VMware.PowerCLI                     Desk
Manifest   11.5.0.14…            VMware.PowerCLI                     Desk
```

You can see PowerCLI v12.1 is available in both versions - along with v11.5. By default PowerShell will not clean up the old modules. You can clean up the old version pretty easily. First we need the full version number of the old v11.5 PowerCLI, then remove it:

```powershell
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI | Format-Table -AutoSize


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version         Name            ExportedCommands
---------- -------         ----            ----------------
Manifest   12.1.0.17009493 VMware.PowerCLI
Manifest   11.5.0.14912921 VMware.PowerCLI


PS C:\Windows\system32> Uninstall-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI | Format-Table -AutoSize


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version         Name            ExportedCommands
---------- -------         ----            ----------------
Manifest   12.1.0.17009493 VMware.PowerCLI
```

We now have the latest PowerCLI version in both PowerShell v5.1 and v7.

### Scenario 2

This next scenario is that I think may be common. You have started using PowerShell v7 and using PowerCLI but using the version that was originally installed under PowerShell v5, not realizing it is in the PowerShell v5 location `C:\Program Files\WindowsPowerShell\Modules`. You read on the [PowerCLI Blog](https://blogs.vmware.com/PowerCLI/) that a new version of PowerCLI is available. You start your PowerShell v7 session and use the normal update commmand:

```powershell
PS C:\Windows\System32> Update-Module -Name VMware.PowerCLI
Update-Module: Module 'VMware.PowerCLI' was not installed by using Install-Module, so it cannot be updated.
```

This is kind of misleading. You did use `Install-Module` but in a PowerShell v5 session. It seems PowerShell v7 can't see this was the case, and thinks the module was manually installed.

In this case again run `Update-Module` in a PowerShell v5 session.

### Scenario 3

This could be something that happens on a shared Jump Server. When the server was setup PowerCLI in PowerShell v7 was installed under the `AllUsers` scope. Then a user hears about an update to PowerCLI so installs the update:

```powershell

PS C:\Windows\System32> Update-Module -Name VMware.PowerCLI
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI | Format-Table -AutoSize


    Directory: C:\Users\a-cwestwater\Documents\PowerShell\Modules

ModuleType Version         PreRelease Name            PSEdition ExportedCommands
---------- -------         ---------- ----            --------- ----------------
Manifest   12.1.0.17009493            VMware.PowerCLI Desk

    Directory: C:\Program Files\PowerShell\Modules

ModuleType Version         PreRelease Name            PSEdition ExportedCommands
---------- -------         ---------- ----            --------- ----------------
Manifest   11.5.0.14912921            VMware.PowerCLI Desk
```

They think to keep up to date that they should remove the old v11.5 module:

```powershell
PS C:\Windows\System32> Uninstall-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI | Format-Table -AutoSize


    Directory: C:\Users\a-cwestwater\Documents\PowerShell\Modules

ModuleType Version         PreRelease Name            PSEdition ExportedCommands
---------- -------         ---------- ----            --------- ----------------
Manifest   12.1.0.17009493            VMware.PowerCLI Desk
```

You see the problem - they have removed PowerCLI for all users. PowerCLI is now just in that users scope.

### Wrap Up

There are other scenarios that are not covered here. I just wanted to show some examples of potential issues that could happen running both PowerShell versions in tandem along with PowerCLI. In my last post I wrapped up by saying:

```
What I would do on a system if you want to start using v7 along with v5 is the following:
- Remove all traces of PowerCLI from v5.1
- Install PowerCLI in v7 using the switch -Scope AllUsers
- Install PowerCLI in v5.1
```

Now I'm not so sure of that statement. I think installing PowerShell under the `AllUsers` scope may be the way to go. I'd love to hear opinions. Reach out to me on [Twitter](https://twitter.com/cwestwater).
