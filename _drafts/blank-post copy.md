---
layout: single
classes: wide
title: ""
# date: YYYY-MM-DD
category: []
excerpt: ""
---
## Introduction

## Testing Windows Server 2022

With a fresh install of Windows Server 2022 it came packaged with the PVSCSI Controller driver v1.3.150.0. I then installed [VMware Tools v11.3.5](https://docs.vmware.com/en/VMware-Tools/11.3/rn/VMware-Tools-1135-Release-Notes.html) which updated the driver version to v1.9.2.0. Now at the time of this blog [VMware Tools v12.0.0](https://docs.vmware.com/en/VMware-Tools/11.3/rn/VMware-Tools-1135-Release-Notes.html) with vmxnet3 driver v1.9.5.0 was available. The reason for installing an older VMware Tools version is I wanted to use Windows Update to download and install vmxnet3 driver v1.9.5.0 without updating tools

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
