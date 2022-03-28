---
layout: single
classes: wide
title: "Windows Server vmxnet3 Drivers and Removing VMware Tools"
date: 2022-03-16
category: [VMware]
excerpt: "What are the implications of removing VMware Tools with a Windows Server OS for the vmxnet3 NIC type?"
---
## Introduction

A question was recently asked on Reddit about the implications of uninstalling VMware Tools and the Ethernet Driver in the guest OS. I immediately thought well of course the drivers are going to be removed, however I wondered if there was any impact when using the drivers that are installed or updated using Windows Update.

## Windows Update and VMware Drivers

Back in 2020 VMware started distributing driver updates for Windows Operating Systems. There is a VMware KB article detailing the driver versions to Operating Systems: [Current Drivers on Windows Update (82290)](https://kb.vmware.com/s/article/82290). It's a pity it was last updated in September 2021 and quite out of date. Not even Windows Server 2022 is listed. I have left feedback for VMware to update it.

Instead you can check all the drivers to OS versions on Windows Update by searching for [vmware](https://www.catalog.update.microsoft.com/Search.aspx?q=vmware). The driver types go back to Windows 7 and the available ones are:

- Graphics/Video
- System
- PVSCSI
- VMXNET3

## A Note on Windows Server 2022

Out of the box Windows Server 2022 comes with the VMware drivers pre-installed. No more having to supply the PVSCSI driver when installing the OS and having a working NIC right away. **Thank you!**

> Note this does not mean you can skip installing VMware Tools. That is still an essential part of your VM build.

## Testing Windows Server 2022

With a fresh install of Windows Server 2022 it came packaged with the vmxnet3 driver v1.8.17.0. I then installed [VMware Tools v11.3.5](https://docs.vmware.com/en/VMware-Tools/11.3/rn/VMware-Tools-1135-Release-Notes.html) which updated the driver version to v1.9.2.0. Now at the time of this blog [VMware Tools v12.0.0](https://docs.vmware.com/en/VMware-Tools/12.0/rn/VMware-Tools-1200-Release-Notes.html) with vmxnet3 driver v1.9.5.0 was available. The reason for installing an older VMware Tools version is I wanted to use Windows Update to download and install vmxnet3 driver v1.9.5.0 without updating tools

I wanted to see if it made a difference if the vmxnet3 driver came from Microsoft via the OS or Windows Update versus installation via VMware Tools. Thus the path was:

1. Fresh OS with vmxnet3 driver v1.8.17.0
2. Apply VMware Tools v11.3.5 which installed vmxnet3 driver v1.9.2.0
3. Apply Windows Update which installed vmxnet3 driver v1.9.5.0

I then uninstalled VMware Tools to see if it would remove the vmxnet3 driver packaged with it. Result was I still had vmxnet3 driver v1.9.5.0 installed.

I had the same result if I then added an additional step of installing VMware Tools v12.0.0 after running Windows Update.

## Testing Windows Server 2016 and 2019

Fresh installs of Windows Server 2016 and 2019 come without any of the VMware drivers. You need to install VMware Tools or sliptream the driver during build time.  I wrote the blog post [Adding VMware Drivers to Server 2012 R2 Boot Media]({{ site.baseurl }}{% post_url 2017-12-03-VMware-Drivers-Server-2012-R2 %}) which describes the process.

I followed a very similar testing process as I used for Windows Server 2022. Once the OS was booted, I installed [VMware Tools v11.3.5](https://docs.vmware.com/en/VMware-Tools/11.3/rn/VMware-Tools-1135-Release-Notes.html) which has the vmxnet3 driver version to 1.9.2.0.  I then ran Windows Update which installed vmxnet3 driver v1.9.5.0. Once This meant I got the initial driver from VMware Tools, but Windows Update installed the latest version. Thus the path was:

1. Fresh OS with no vmxnet3 driver
2. Apply VMware Tools v11.3.5 which installed vmxnet3 driver v1.9.2.0
3. Apply Windows Update which installed vmxnet3 driver v1.9.5.0

I then uninstalled VMware Tools and the vmxnet3 driver was removed, losing Ethernet connectivity from the VM.

## Wrap Up

It seems pretty obvious initially as prior to Windows Server 2022 if you removed VMware Tools the VMware specific drivers were removed. Now with Windows Server 2022 this is no longer the case. It would have to be a very special reason to remove VMware tools from a VM, but please be aware of the implications when using any OS prior to Windows  Server 2022.
