---
layout: single
title: "Adding VMware Drivers to Windows Server 2019 ISO"
# date: YYYY-MM-DD
category: [VMware,Microsoft]
excerpt: "How to add VMware Tools Drivers to a Windows Server 2019 ISO"
---
## Introduction

Back in 2017 I wrote the post [Adding VMware Drivers to Server 2012 R2 Boot Media]({{ site.baseurl }}{% post_url 2017-12-03-VMware-Drivers-Server-2012-R2 %}) which detailed adding the VMXNET3 and PVSCSI drivers to a 2012 R2 ISO. Times have changed and I now use 2019 in the lab.

This post runs through the same process but crucially at the end the ISO creation process is different.

## Folder Structure

Create the following folder structure:

- C:\Images - top level folder and where the new ISO fill will be placed
- C:\Images\Mount - where DISM will mount the .wim files
- C:\Images\Drivers - where the extracted VMware Tools files will be placed
- C:\Images\Tools - where the downloaded VMware Tools package will be placed

## VMware Tools

The first thing is to download VMware tools. The [VMware Tools Operating System Specific Packages](https://www.vmware.com/support/packages.html) is an excellent resource for finding all the Tools package downloads. The latest VMware Tools can always be accessed at [https://packages.vmware.com/tools/releases/latest/](https://packages.vmware.com/tools/releases/latest/) where each OS is listed. At the time of this blog post the latest VMware Tools version is [11.1.1](https://packages.vmware.com/tools/releases/latest/windows).

You have two choices here. Either download the ISO - [VMware-tools-windows-11.1.1-16303738.iso](https://packages.vmware.com/tools/releases/latest/windows/VMware-tools-windows-11.1.1-16303738.iso) and extract the contents, or save a step and download the Windows x64 executable - [VMware-tools-11.1.1-16303738-x86_64.exe](https://packages.vmware.com/tools/releases/latest/windows/x64/VMware-tools-11.1.1-16303738-x86_64.exe).

Once you have the .exe downloaded place it in `C:\Images\Tools` then open a command prompt and browse to this folder. Run the command to start the extraction process:

~~~ dosbatch
C:\Images\Tools>VMware-tools-11.1.1-16303738-x86_64.exe /A /P C:\Images\Drivers
~~~

The VMware Tools Setup gui will appear. Click Next > and then choose the extract location in the next screen - which should be `C:\Images\Drivers`:

![VMware Tools Extract]({{ site.url }}/assets/images/VMware-Tools-Extract-01.png)

Click Install. This does not install VMware Tools, it just extract the files to `C:\Images\Drivers`. The drivers are located in the subfolder `C:\Images\Drivers\VMware\VMware Tools\VMware\Drivers`:

![VMware Tools Extract]({{ site.url }}/assets/images/VMware-Tools-Extract-02.png)

You can see all the drivers are there including things like the mouse, audio, vss, etc. drivers and all we are really interested in and the `vmxnet3` and `pvscsi` folders. However I'm just going to point DISM at the `Drivers` folder and grab all the drivers it wants. You can take the time to remove the folders not necessary if you want.

We now have the drivers ready to integrate into Windows.

## Extract Windows ISO

As this is my lab I use the Microsoft Evaluations 2019 ISO download. You can get it from the [Microsoft Evaluations Center](https://www.microsoft.com/en-gb/evalcenter/evaluate-windows-server-2019) which gives you 180 days to use the OS.

> There is a trick to extend this evaluation to 3 years. See this [blog post](https://sid-500.com/2017/08/08/windows-server-2016-evaluation-how-to-extend-the-trial-period/)

After downloading the ISO, extract it to `C:\Images\ISO` using a utility such as [7-Zip](https://www.7-zip.org/)

## 

## WAIK