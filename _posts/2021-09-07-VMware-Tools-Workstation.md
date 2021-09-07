---
layout: single
classes: wide
title: "VMware Workstation and VMware Tools"
date: 2021-09-07
category: [VMware]
excerpt: "How to keep VMware Tools up to date in a VMware Workstation Installation"
---
## Introduction

Traditionally VMware Tools was tied to a release of ESXi but that has changed where they are released on a regular cadence. There are various ways you can deploy and manage Tools on a vSphere installation but it is a manual process in Workstation.

By manual process I mean to update on Workstation you would have to mount the new Tools ISO to the VM and kick off an installation inside the OS. In this post I will detail how to keep Tools up to date in a Workstation installation.

This post was written using Workstation v16.1.2, VMware Tools 11.3.0 and the guest OS was Windows Server 2022.

### Download VMware Tools

First we need to download the latest VMware Tools. I covered this in my previous post [Silent Install VMware Tools]({{ site.baseurl }}{% post_url 2020-10-13-VMware-Tools-Drivers %}).

The latest VMware Tools can always be accessed at [https://packages.vmware.com/tools/releases/latest/](https://packages.vmware.com/tools/releases/latest/). We need to download the file [VMware-tools-windows-11.3.0-18090558.iso](https://packages.vmware.com/tools/releases/11.3.0/windows/) [Direct Link to the iso](https://packages.vmware.com/tools/releases/11.3.0/windows/VMware-tools-windows-11.3.0-18090558.iso).

### VMware Workstation Tools Location

By default, the Tools iso that comes as part of a Workstation install can be found at `C:\Program Files (x86)\VMware\VMware Workstation`. In that folder there are several iso files:

- linux.iso
- linuxPreGlibc25.iso
- netware.iso
- solaris.iso
- VirtualPrinter-Linux.iso
- VirtualPrinter-Windows.iso
- windows.iso
- winPre2k.iso
- winPreVista.iso

In this case, I am using a Windows Server 2022 VM so the iso file I want to look at is `windows.iso`. After installing the 2022 VM and starting a Tools installation:

![Workstation Tools Installation]({{ site.url }}/assets/images/workstation-tools-01.png)

and letting the Tools splash screen come up we can see out of the box Workstation v16.1.2 comes with VMware Tools v11.2.6:

![Workstation Tools Installation]({{ site.url }}/assets/images/workstation-tools-02.png)

As stated above, Tools v11.3.0 is available, so we want to have that as the version applied when installing Tools in Workstation.

### Updating Built-in Tools ISO

This is very simple:

1. Browse to `C:\Program Files (x86)\VMware\VMware Workstation`
2. Rename `windows.iso` to `windows.iso.original`
3. Rename the downloaded file `VMware-tools-windows-11.3.0-18090558.iso` to `windows.iso`
4. Move the new `windows.iso` to `C:\Program Files (x86)\VMware\VMware Workstation`

Now when we start a Tools installation we see:

![Workstation Tools Installation]({{ site.url }}/assets/images/workstation-tools-03.png)

When a new version of VMware Tools is released simply follow the procedure above again to keep it up to date.

### Updating VMware Tools

So what I have detailed above is great for new VMs that have the first install of Tools, but what about exisiting VMs? After replacing the `windows.iso` and right clicking on the VM we can now see:

![Workstation Tools Installation]({{ site.url }}/assets/images/workstation-tools-04.png)

This mounts the ISO in the VM and then running `setup64.exe` in the guest it shows the expected v11.3.0.

## Wrap Up

In the past I had not given much thought to VMware Tools in Workstation and just used the version that came with it. Now with Tools being updated more frequently to remediate vulnerabilities I will be using this procedure to keep on top of ensuring they are up to date.