---
layout: single
title: "VMware Workstation 15.5 Installation Issue"
date: 2019-09-20
category: [VMware]
excerpt: "I hit a strange problem updating my copy of VMware Workstation to 15.5. Here is the manual fix"
---

## Introduction

VMware released Workstation 15.5 today ([here is the announcement](https://blogs.vmware.com/workstation/2019/09/workstation-15-5-available-now.html)). I very eagerly tried to update my work machine and personal machines and hit a strange error that seems related to Visual C++ Redistributable not being installed properly or up to date. Here is the workaround.

## The Issue

Both machines are Windows 10 (1903 and 1703 both fully patched for that version) running VMware Workstation 15.1.0. When checking for a new version using `Help...Software Updates` it was correctly seeing v15.5 was available.

It starts to install and throws this prompt:

![Initial prompt]({{ site.url }}/assets/images/Workstation-15.5-Error-01.png)

No problem - the VC Redistributables often need a reboot so I did it.

On restart I kicked off the installer again and got the same issue! It seemed to not be able to get past this prompt. Rebooting brings it back and clicking `No` cancels the update.

There is a [link in the error message](https://kb.vmware.com/s/article/55798). I checked that but that seems specific to Tools version 10.3.

Workstation 15.5 comes with Tools v11 as detailed by [@Kev Johnson](https://twitter.com/kev_johnson) in this [blog post](https://blogs.vmware.com/vsphere/2019/09/vmware-tools-11-0-out-now.html). Also it mentions needing Microsoft Visual C++ 2017 Redistributable version 14.x which I had installed.

## The Workaround

As it was a new release I wondered if I had the latest redistributable. I was running `Microsoft Visual C++ 2015-2019 Redistributable 14.20.27508`. Checking online version `14.22.27821` is available.

To fix go to the latest download for the package - [The latest supported Visual C++ downloads](https://support.microsoft.com/en-gb/help/2977003/the-latest-supported-visual-c-downloads)

There you will see the x86 and x64 versions of the installer. Download both and apply. I started with the x86 version then the x64, with a reboot after each one. Here is a screenshot after doing the x86 version:

![VSC Update]({{ site.url }}/assets/images/Workstation-15.5-Error-02.png)

After the reboot I kicked off the update from inside Workstation and it proceeded no problem.

![VSC Update]({{ site.url }}/assets/images/Workstation-15.5-Error-03.png)

## Wrap Up

I thought this was maybe me as I couldn't find anything online of the issue. Unfortunately the VMTN forums were down for maintenance so I couldn't check there.

Then I stumbled on a [Reddit post](https://www.reddit.com/r/vmware/comments/d6wj9d/unable_to_update_vmware_workstation_pro_15_to_155/) about it. There are a couple of other suggestions for a fix but I cannot test them as I had already fixed it and upgraded.

Hope this helps someone out there. If I seem VMware with any updates about this issue will update this post.
