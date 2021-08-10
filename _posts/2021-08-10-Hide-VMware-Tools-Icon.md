---
layout: single
classes: wide
title: "How to Hide the VMware Tools Icon"
date: 2021-08-10
category: [VMware]
excerpt: "The VMware Tools icon is placed in the system tray after installation. How to disable the icon"
---
## Introduction

When VMware Tools is installed on a Windows Guest OS it places an icon in the System Tray:

![VMware Tools Icon]({{ site.url }}/assets/images/VMware-Tools-Icon-01.png)

It actually doesn't do much other than let you see the installed version and the state of the service:

![VMware Tools About]({{ site.url }}/assets/images/VMware-Tools-About.png)

I personally like to hide all unnecessary 'cruft' from an OS build and I class this icon in the system tray as one of those examples. In this post I will detail how to remove the icon using a variety of methods.

At the time of this blog post VMware Tools was version 11.2.6 and this was performed on a Windows Server 2019 VM.

### Manual Icon Removal

As you can see from the previous screenshot there is an option to disable the icon by right clicking the system tray icon.

![VMware Tools Icon Manual Removal]({{ site.url }}/assets/images/VMware-Tools-Remove-Manually.png)

This does indeed remove the icon - __for the current user only__. Not ideal as I want to remove it for whoever logs in.

### Manual Registry Key Icon Removal

When you manually remove the icon what actually changes? A new value is added to the registry under `HKEY_CURRENT_USER\Software\VMware, Inc.\VMware Tools`. The value added is `ShowTray` which is a `REG_DWORD` with Data `0`:

![VMware Tools Icon Current User Registry]({{ site.url }}/assets/images/VMware-Tools-Current-User-Registry.png)

Again, this is not ideal as it is only for the current user. To remove the icon for all users add the same value/data but in `HKEY_LOCAL_MACHINE\SOFTWARE\VMware, Inc.\VMware Tools`:

![VMware Tools Icon All Users Registry]({{ site.url }}/assets/images/VMware-Tools-All-Users-Registry.png)

### Command Prompt Registry Key Icon Removal

So we know how to manually add the correct entry to the Registry to remove the VMware Tools icon from the System Tray for all users. I do not want to manually perform this on all machines I build, so we can use a simple one liner at a command prompt to add the key:

```dosbatch
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\VMware, Inc.\VMware Tools" /v ShowTray /t REG_DWORD /d 0
```

This uses the built in command [reg add command](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/reg-add) that is part of the OS. It is broken up as so:

1. `reg add "HKEY_LOCAL_MACHINE\SOFTWARE\VMware, Inc.\VMware Tools"` - this is the command and the key name we want to work in
2. `/v ShowTray` - this is the value name of the registry entry we want to add
3. `/t REG_DWORD` - specify what type of registry entry being added
4. `/d 0` - set the regsitry DWORD data to 0

Now we have a working entry at the command line this could be placed in a batch file to be run on the machine on first boot. Another way to deploy this would be as part of a [Packer Provisioner](https://www.packer.io/docs/provisioners/windows-shell) when a template is built:

```terraform
provisioner "windows-shell" {
  inline = ["reg add "HKEY_LOCAL_MACHINE\\SOFTWARE\\VMware, Inc.\\VMware Tools" /v ShowTray /t REG_DWORD /d 0"]
}
```

#### PowerShell Registry Key Icon Removal

If you prefer PowerShell, the cmdlet [Set-ItemProperty](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/set-itemproperty) can be used to add the `ShowTray` value and Data of `0` to the correct path in the registry:

```posh
Set-ItemProperty -Path 'HKLM:\SOFTWARE\VMware, Inc.\VMware Tools' -Name 'ShowTray' -Value 0
```

Again this could be pushed using many different tools but again I like to do this when building my template in Packer using the [PowerShell Provisioner](https://www.packer.io/docs/provisioners/powershell)

```terraform
provisioner "powershell" {
  inline = ["Set-ItemProperty -Path 'HKLM:\\SOFTWARE\\VMware, Inc.\\VMware Tools' -Name 'ShowTray' -Value 0"]
}
```

### Wrap Up

A short and simple post this time, but I felt it was worth documenting, especially when I work on my Packer templates and I need to check how to remove the icon.