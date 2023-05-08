---
layout: single
classes: wide
title: "Packer vmware-iso Template Import Issue"
date: 2023-05-08
category: [VMware]
excerpt: "How to fix and issue when deploying a Windows Server 2022 template in VMware Workstation generated with Packer's vmware-iso plugin"
---
## Introduction

I have finally completed my Packer Build for Windows Server using the [vmware-iso](https://developer.hashicorp.com/packer/plugins/builders/vmware/iso) plugin which creates templates in VMware Workstation. However, I found an issue when trying to use the generated ova or ovf file. This post will detail the importance of keeping software up to date!

## Versions

This post was written using the following versions:

- Packer v1.8.6
- vmware-iso plugin v1.0.7
- VMware Open Virtualization Format Tool v4.4.3
- VMware Workstation v17.01

## The Issue

I was finishing my Packer builds for VMware Workstation and successfully created and tested Windows Server 2016 and 2019. However, I noticed a problem after the OVA or OVF was successfully created. The template imports successfully:

![OVA Import]({{ site.url }}/assets/images/ova-import-error.png)

Nonetheless on booting the VM the following is shown:

![Boot Error]({{ site.url }}/assets/images/boot-error.png)

and then the VM goes into the EFI Boot Manager:

![Boot Manager]({{ site.url }}/assets/images/boot-manager.png)

The VM will simply not boot correctly.

## The Workaround

After investigation, I stumbled upon the issue with the imported VM. Looking at the VM Settings and then the General Settings I saw the Guest operating system was set to Other/Other:

![Bad VM Settings]({{ site.url }}/assets/images/bad-vm-settings.png)

This should be set to Microsoft Windows/Windows Server 2022:

![Good VM Settings]({{ site.url }}/assets/images/good-vm-settings.png)

Now the VM boots correctly - but why?

## The Fix

This is ultimately a tale of keeping your software up to date. It turns out I was running [v4.4.3](https://developer.vmware.com/web/tool/4.4.0/ovf) of the OVF Tool. Upon further research I found out [v4.6.0](https://developer.vmware.com/web/tool/4.6.0/ovf-tool) was available. As soon as I installed the new version and re-ran the Packer build importing the OVA worked correctly:

![Good VM Settings]({{ site.url }}/assets/images/good-vm-settings.png)

When searching Google for the [term OVF Tool](https://www.google.com/search?q=ovf+tool) the first link brings up the page for [v4.4.0](https://developer.vmware.com/web/tool/4.4.0/ovf) but notice on the page the statement:

> Note: OVF Tool 4.5 for vSphere 8.0 is here at this location, not in the drop-down menu.

Not sure why the drop-down can't have the latest version in it.

## Wrap Up

I read the release notes for OVF Tool and could not see the details of what Operating Systems it supports for the metadata of the OS Config. Maybe VMware could incorporate that.

Anyway, a lesson in keeping your software up to date!
