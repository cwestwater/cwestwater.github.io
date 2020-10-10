---
layout: single
title: "Silent Install VMware Tools"
#date: YYYY-MM-DD
category: [VMware]
excerpt: "How to download, extract, and silent install VMware Tools on Windows"
---

## Introduction

VMware Tools are critical for the effective use of virtual machines. From the VMware website:

> VMware Tools is a set of services and modules that enable several features in VMware products for better management of, and seamless user interactions with, guest operating systems.

In this post I will details how to download, extract, and manually VMware Tools and the drivers contained in it.

## Download

The first thing is to download VMware tools. The [VMware Tools Operating System Specific Packages](https://www.vmware.com/support/packages.html) is an excellent resource for finding all the Tools package downloads. To see a list of all release back to v10.0.0 you can visit [https://packages.vmware.com/tools/releases/](https://packages.vmware.com/tools/releases/).

The latest VMware Tools can always be accessed at [https://packages.vmware.com/tools/releases/latest/](https://packages.vmware.com/tools/releases/latest/). At the time of this blog post the latest VMware Tools version is 11.1.5.

Under the version of tools you need, there are download options for the following operating systems:

- rhel5 - Red Hat Linux 5
- rhel6 - Red Hat Linux 6
- sles11sp0 - SUSE Linux 11
- sles11sp1 - SUSE Linux 11 SP1
- sles11sp2 - SUSE Linux 11 SP2
- sles11sp3 - SUSE Linux 11 SP3
- sles11sp4 - SUSE Linux 11 SP4
- ubuntu - Ubuntu Linux variants
- windows - Windows Operating Systems

For Linux VMs it is generally it is best to use open-vm-tools which is built into most major distributions. See the [open-vm-tools GitHub](https://github.com/vmware/open-vm-tools) page for more details.

Windows is a more manual process. VM Tools is not part of the OS and doesn't ship as part of it, so it needs to be installed after the operating system is installed.

If you browse the package site and browse down in to the latest Windows release you are shown the following (note at the time of this blog post the version of VM Tools is 11.1.5):

![VMware Tools Packages]({{ site.url }}/assets/images/VMware-Tools-Packages.png)

You can see three files and two directories. Unfortunately in Chrome the full file path is cut off for the files. Only by hovering over the link can you see the full file name. Once you know what each file as and its corresponding size it's easy to glance at the list and pick the right file.

1. 138M file is an ISO of all the VMware Tools files
2. 2.2M file is a [SHA](https://www.file-extension.org/extensions/sha#) file
3. 3k file is a SIG file which can be used to [verify the digital signature](https://kb.vmware.com/s/article/2005832) of the download

Unless you are verifying the download just grab the 138MB ISO file.

The two folders `x64` and `x86` contain standalone `.exe` files for use in the OS. Just pick your flavour:

- VMware-tools-11.1.5-16724464-i386.exe - 32 bit Windows
- VMware-tools-11.1.5-16724464-x86_64.exe - 64 bit Windows

### ISO File

The ISO file is the best option if you want to mount the ISO to the VM and install VMware Tools. Mounting the ISO exposes the following files/folders:

![VMware Tools ISO Files]({{ site.url }}/assets/images/VMware-Tools-ISO-Files.png)

You can see there is an `autorun.inf` there so the install may launch automatically when the ISO is mounted. If not, launch `setup.exe` for 32 bit Windows, and `setup64.exe` for 64 bit Windows.

### Standalone .exe Files

The standalone `VMware-tools-*.exe` files run exactly the same installation as the `setup.exe` and `setup64.exe` files packaged in the ISO file. In fact if you look at the file size for the 32 and 64 bit files they match:

![VMware Tools exe Files]({{ site.url }}/assets/images/VMware-Tools-exe-Files.png)

I tend to use this package if I do ad-hoc updates to VMware Tools on an existing OS install.

## Installation

The traditional way of installing VMware Tools is using `Install/Upgrade VMware Tools` in the vSphere client. Now that VMware Tools is decoupled from the ESXi build version this may not be applicable for your case. Come back for a future blog post where I will show a couple of ways to deploy this way.

What seems to be coming more common is to use something like SCCM to push out VMware Tools to the OS. This will require using silent install deployment options using command line switches.

You can get some idea of what is required by calling the installation .exe (setup.exe/setup64.exe for the ISO download or the VMware-tools-\*.exe for the standalone download) from a command line with the switch `setup64.exe /?`

![VMware Tools Setup Switches]({{ site.url }}/assets/images/VMware-Tools-setup-switches.png)

I want to make the install silent, so the `/s` line has the information we need. The required switches are `/s /v /qn` and the MSI arguments.

- `/s` - hide installation dialog box
- `/v` - pass MSI parameters

Next is the MSI arguments:

- `/qn` - fully silent install
- REBOOT=R - do NOT reboot
- ADDLOCAL=ALL - install all components
- REMOVE=\* - do not install specific components

It seems kind of strange that if you do not want to install some component you have to first specify to install everything then remove the component. Just one of those things when they designed msi packages I guess.

So how do you know what the [individual component](https://docs.vmware.com/en/VMware-Tools/11.1.0/com.vmware.vsphere.vmwaretools.doc/GUID-E45C572D-6448-410F-BFA2-F729F2CDA8AC.html) names are?

- Audio - Audio driver
- Bootcamp - Mac BootCamp Driver
- MemCtl - Virtual Memory Control Driver
- Mouse - Mouse Driver
- PVSCSI - Paravirtual SCSI driver
- SVGA - SVGA display Driver
- Sync - Filesystem Driver to allow snapshot based backups for older than Windows Server 2003 OSs
- ThinPrint - Allows printers on host OS to be used in VM. Depreciated from vSphere 5.5 onwards
- VMCI - Virtual Machine Communication Interface driver
- Hgfs - Shared Folders driver. For Workstation, Player and Fusion only
- VMXNet - VMXNet network driver
- VMXNet3 - VMXNet3 network driver
- FileIntrospection - NSX File Introspection driver
- NetworkIntrospection - NSX Network Introspection driver
- VSS - VSS based snapshot backups drivers for Windows Server 2003 and newer
- AppDefense - AppDefence component
- Perfmon - WMI performance logging driver

Some of these options obviously depend on the OS version.

An example install at the command line is this:

```shell
VMware-tools-11.1.5-16724464-x86_64.exe /S /v"/qn REBOOT=R ADDLOCAL=ALL REMOVE=AppDefense,FileIntrospection,NetworkIntrospection,Hgfs" /l C:\Temp\VMwareToolsInstall.log
```

Let's break this down:

- This installs the Windows 64-bit version of VMware Tools
- The installer is silent and hides the dialog box
- Suppress a reboot
- Install all options except AppDefense, NSX File and Network Introspection, and Shared Folders
- Output a log file to C:\Temp

You can use this now at the command line or packed up in a third party tool for mass deployment.

## Wrap Up

VMware Tools is a critical part of your vSphere environment. This should give you the knowledge to integrate the deployment of VMware tools in an silent fashion using a simple command line script.
