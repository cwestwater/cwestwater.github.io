---
layout: single
classes: wide
title: "PowerCLI with PowerShell 5.x and 7.x - Part 1"
date: 2020-10-19
category: [VMware]
excerpt: "How does the PowerCLI module install work on a computer with both PowerShell 5.x and 7.x"
---

## Introduction

With the introduction of PowerShell v7 we are in a hybrid situation where Windows systems could potentially have both v5.x and v7.x of PowerShell installed. v5.x is part of the Windows 10 default install, but people should start to be migrating to [PowerShell 7.x](https://github.com/PowerShell/PowerShell) release versions.

How does this relate to PowerCLI? You may find that you think you have access to PowerCLI between versions, but this may not be the case. This post will look into this.

### PowerShell Versions

PowerShell v5.x is now considered the 'legacy' version of PowerShell. Microsoft have stated that the v5.x will only receive security updates and bug fixes. All development is going into the cross platform PowerShell v7.x release. This version of PowerShell can be used on Windows, Linux and macOS.

> I am a Windows guy so this post will focus on that platform. At the time of this post PowerShell is at versions v5.1 and v7.0.3

PowerShell v7.x does not come by default with any Windows OS. It is a manual install you need to perform. You can download from the [PowerShell GitHub page](https://github.com/PowerShell/PowerShell#get-powershell). Once this is installed, you might start to notice that modules might not be shared between the two versions of PowerShell.

### Module Locations

PowerShell modules can be in various locations which also depends on the version you are using. Using the command `$env:PSModulePath -split ';'` on each version lists the default module path locations:

| PowerShell v5.1                                           | PowerShell v7                                      |
| --------------------------------------------------------- | -------------------------------------------------- |
| C:\Program Files\WindowsPowerShell\Modules                | C:\Program Files\WindowsPowerShell\Modules         |
| C:\Windows\system32\WindowsPowerShell\v1.0\Modules        | C:\Windows\system32\WindowsPowerShell\v1.0\Modules |
| C:\Users\a-cwestwater\Documents\WindowsPowerShell\Modules |                                                    |
|                                                           | C:\Users\a-cwestwater\Documents\PowerShell\Modules |
|                                                           | C:\Program Files\PowerShell\Modules                |
|                                                           | C:\Program Files\Powershell\7\Modules              |

You can see only two locations are common between the two versions.

- `C:\Program Files\WindowsPowerShell\Modules` - modules installed in v5.1 available to all user accounts on the computer
- `C:\Windows\system32\WindowsPowerShell\v1.0\Modules` - this location is reserved for modules that ship with Windows. Do not install modules to this location

The other different locations are:

- `C:\Users\a-cwestwater\Documents\WindowsPowerShell\Modules` - modules installed in v5.1 under the user scope
- `C:\Users\a-cwestwater\Documents\PowerShell\Modules` - modules installed in v7 under the user scope
- `C:\Program Files\PowerShell\Modules` - modules installed in v7 to all users on the computer
- `C:\Program Files\Powershell\7\Modules` - core PowerShell v7 modules

So in summary there are many locations modules can be located.

### PowerCLI Module Installation

This is very easy. PowerCLI is hosted on the PowerShell Gallery. By default the PowerShell Gallery is set to 'untrusted' so when you try to install PowerCLI from the Gallery you get the message:

```powershell
Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
```

You can just press `A` or `Y` to continue but it gets old fast. To set the PowerShell Gallery to trusted run this command at an elevated PowerShell prompt:

```powershell
PS C:\Windows\System32>> Set-PSRepository -Name PSGallery -InstallationPolicy Trusted
```

and then to check:

```powershell
PS C:\Windows\System32>> Get-PSRepository -Name PSGallery

Name                      InstallationPolicy   SourceLocation
----                      ------------------   --------------
PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
```

This is a global setting across PowerShell versions, so it does not matter if done in 5.1 or 7.0.3.

Installing PowerCLI is very simple:

```powershell
PS C:\Windows\System32>> Install-Module -Name VMware.PowerCLI
```

### Default Install-Module Behaviour

While investigating this I ran through various install combinations on a clean install of Windows 10. I had the built in PowerShell v5.1 version, then I installed v7. I noticed something different in the default behaviour for module installation between v5.1 and v7.

I installed PowerCLI from an elevated PowerShell v5.1 session and I didn't specify a scope (`AllUsers` vs `CurrentUser`). I then checked the module was installed:

```powershell
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   12.1.0.... VMware.PowerCLI
```

You can see this was installed to the legacy All Users on the computer location `C:\Program Files\WindowsPowerShell\Modules` so if I check in a v7 session for PowerCLI:

```powershell
PS C:\Windows\System32>> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\WindowsPowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   12.1.0.17…            VMware.PowerCLI                     Desk
```

This shows v7 can see the module for use as it too looks at the same module folder.

So next I removed PowerCLI by deleting all the PowerCLI folders in that location. This mean I was back to no PowerCLI being installed on either version. I then started an elevated v7 session and ran the same install command:

```powershell
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Users\a-cwestwater\Documents\PowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   12.1.0.17…            VMware.PowerCLI                     Desk
```

This time it installed under the `CurrentUser` scope to the folder `C:\Users\a-cwestwater\Documents\PowerShell\Modules`. Checking on a v5.1 session for PowerCLI shows:

```powershell
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI
PS C:\Windows\system32>
```

No PowerCLI available in v5.1.

This is a key difference it seems between v5.1 and v7. The default scope for install is different. This is equivalent to

- v5.1 - `Install-Module -Name VMware.PowerCLI -Scope AllUsers`
- v7 - `Install-Module -Name VMware.PowerCLI -Scope CurrentUser`

Again I removed all the PowerCLI modules and installed PowerCLI on a v7 sessions this time specifiying `-Scope AllUsers`, amd then checking the install location:

```powershell
PS C:\Windows\System32> Install-Module -Name VMware.PowerCLI -Scope AllUsers
PS C:\Windows\System32> Get-Module -ListAvailable -Name VMware.PowerCLI


    Directory: C:\Program Files\PowerShell\Modules

ModuleType Version    PreRelease Name                                PSEdition ExportedCommands
---------- -------    ---------- ----                                --------- ----------------
Manifest   12.1.0.17…            VMware.PowerCLI                     Desk
```

You can see this time PowerCLI is installed to the v7 All Users module location `C:\Program Files\PowerShell\Modules`. This is **not** a module path in v5.1 so it will not be available there:

```powershell
PS C:\Windows\system32> Get-Module -ListAvailable -Name VMware.PowerCLI
PS C:\Windows\system32>
```

### My Recommendation

This shows you could be confused when moving between the different PowerShell installations if you have both installed as regards to modules availability. I have focussed on PowerCLI but this is the same for any module.

You might think the easiest thing to do is install PowerCLI from a v5.1 session to the `AllUsers` scope and it will be available for both versions. However what happens if in a future release of PowerShell v7 Microsoft drop the legacy `C:\Program Files\WindowsPowerShell\Modules`? PowerShell v7 will then lose access to PowerCLI. PowerShell v7 is designed to run side-by-side with v5.1 and so maintains it's own set of Module paths.

What I would do on a system if you want to start using v7 along with v5 is the following:

- Remove all traces of PowerCLI from v5.1
- Install PowerCLI in v7 using the switch `-Scope AllUsers`
- Install PowerCLI in v5.1

### Wrap Up

This can seem confusing. It's just what we have to live with while Microsoft still 'support' two versions of PowerShell.

Coming up in the next post is what happens if you mix the installed versions of PowerCLI between v5.1 and v7.
