---
layout: single
classes: wide
title: "PowerCLI with PowerShell 5.x and 7.x - Part 2"
# date: YYYY-MM-DD
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

~~~powershell
Install-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921
~~~

### The Scenario

The scenario I am testing is one that may be pretty common. I have an older version of PowerCLI installed in v5.1:

~~~powershell
PS C:\Windows\system32> Install-Module -Name VMware.PowerCLI -RequiredVersion 11.5.0.14912921

PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   11.5.0.... VMware.PowerCLI
~~~

As found in the [previous post]({{ site.baseurl }}{% post_url 2020-10-19-PowerShell-PowerCLI-Part-1 %}) by default PowerShell v5.1 installs modules to the `AllUsers` scope so the modules are located in `C:\Program Files\WindowsPowerShell\Modules` which is a module path in PowerShell v7. So if we check the availability of PowerCLI v11.5 in PowerShell v7:

~~~powershell
PS C:\Windows\System32> get-module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   11.5.0.14…            VMware.PowerCLI                     Desk
~~~