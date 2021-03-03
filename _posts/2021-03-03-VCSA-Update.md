---
layout: single
classes: wide
title: "VCSA Update Problem"
date: 2021-03-03
category: [VMware]
excerpt: "A tale of making sure you have a valid upgrade path"
---
## Introduction

Recently I was upgrading a bunch of VCSAs & PSCs and hit a problem that reminded me to make sure I have a valid upgrade path in place. This is a post as a reminder for all to make sure you check, and check again before performing any upgrade.

I'll also show how to verify that a VCSA patch is a valid version to upgrade **to** depending on what version you are coming **from**.

## Scenario

There was a specific upgrade that needed to be done:

- Source was VMware vCenter Appliance running 6.5 Update 1g (build 8024368)

![VCSA 6.5U1g]({{ site.url }}/assets/images/VCSA-6.5U1g.png)

- Target version is 6.5 Update 2h (build 13834586)

Yes I know this is from a very old version to another old version but we had to stick with that for reasons.

> Bonus link: I use the [Correlating build numbers and versions of VMware products (1014508)](https://kb.vmware.com/s/article/1014508) page to look up build numbers of VMware products all the time. Bookmark that link

I had checked the [VMware Product Interoperability Matrix](https://interopmatrix.vmware.com/#/Upgrade?productId=2) and it looked like I had a good upgrade:

![Interoperability Matrix]({{ site.url }}/assets/images/vcsa-interop-matrix.png)

## Attempted Upgrade from VAMI

> Link: [VMware Product Patches](https://my.vmware.com/group/vmware/patch#search) download site (VMware account required)

First of all I mounted the file `VMware-vCenter-Server-Appliance-6.5.0.24100-13834586-patch-FP.iso` to the appliance and logged into the VAMI interface. Navigate to the Update section, then check for updates from the mounted ISO. Looked good:

![VCSA Update]({{ site.url }}/assets/images/VCSA-Update-Check.png)

Hit `Install Updates` then `Install CDROM Updates` which started the update process:

![VCSA Update Failed]({{ site.url }}/assets/images/VCSA-failed-update.png)

Hmm. Show Details does not exactly help either:

![VCSA Update Failed Details]({{ site.url }}/assets/images/VCSA-failed-update-details.png)

Feedback to VMware - not exactly much detail is it? Some searching pointed to an expired appliance root password but ours was still valid.

## To the Command Line

If the GUI wasn't much help off to the command line I went. I connected via SSH to the appliance and used the `software-packages` command to see what was going on.

> See [Patching the vCenter Server Appliance by Using the Appliance Shell](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.upgrade.doc/GUID-6751066A-5D4E-47AC-A6A4-5E90AEC63DAA.html) for full details of the commands used

First I staged the packages from the ISO:

```shell
Command> software-packages stage --iso --acceptEulas
 [2021-02-27T13:11:36.058] : ISO mounted successfully
 [2021-02-27T13:11:36.058] : Staged 153 packages.
 [2021-02-27T13:11:36.058] : Verifying staging area
 [2021-02-27T13:11:36.058] : ISO unmounted successfully
 [2021-02-27T13:11:36.058] : Staging process completed successfully
 ```

Next was to check what it ready for installation:

```shell
Command> software-packages list --staged
 [2021-02-27T13:12:49.058] :
        category: Security
        kb: https://docs.vmware.com/en/VMware-vSphere/6.5/rn/vcenter-server-appliance-photonos-security-patches.html
        vendor: VMware, Inc.
        name: VC-6.5.0U2h-Appliance-FP
        tags: [u'']
        summary: Patch for vCenter Server Appliance 6.5 with security fixes for PhotonOS
        version_supported: [u'6.5.0.20000', u'6.5.0.20100', u'6.5.0.21000', u'6.5.0.22000', u'6.5.0.23000', u'6.5.0.23100', u'6.5.0.23200', u'6.5.0.24000']
        thirdPartyInstallation: False
        releasedate: May 30, 2019
        TPP_ISO: False
        version: 6.5.0.24100
        buildnumber: 13834586
        rebootrequired: True
        productname: VMware vCenter Server Appliance
        eulaAcceptTime: 2021-02-27 13:11:36 UTC
        severity: Critical
```

Next was an attempt to upgrade again:

```shell
Command> software-packages install --iso --acceptEulas
 [2021-02-27T13:15:02.058] : ISO mounted successfully
 [2021-02-27T13:15:02.058] : Staged 153 packages.
 [2021-02-27T13:15:02.058] : Verifying staging area
 [2021-02-27T13:15:02.058] : ISO unmounted successfully
 [2021-02-27T13:15:02.058] : Validating software update payload
 [2021-02-27T13:15:02.058] : Unsuported version of patch selected.
 [2021-02-27T13:15:02.058] : Installation process failed
```

At least this time I had the actual problem: `Unsuported version of patch selected`. Reading back to the output from the list of staged packages this was the key:

```shell
version_supported: [u'6.5.0.20000', u'6.5.0.20100', u'6.5.0.21000', u'6.5.0.22000', u'6.5.0.23000', u'6.5.0.23100', u'6.5.0.23200', u'6.5.0.24000']
```

Remember I am on `6.5.0.15000` so not at a supported version for the Update 2h to work. I need to be on at least 6.5 Update 2 for Update 2h to install.

![Facepalm]({{ site.url }}/assets/images/picard-facepalm.jpg)

## Update 2, Then Update 2h Install

Back to the [download](https://my.vmware.com/group/vmware/patch#search) site and I grabbed `VMware-vCenter-Server-Appliance-6.5.0.20000-8307201-patch-FP.iso` and mounted that to the appliance. Checking this time:

```shell
Command> software-packages stage --iso --acceptEulas
 [2021-02-27T13:27:22.058] : ISO mounted successfully
 [2021-02-27T13:27:22.058] : Staged 95 packages.
 [2021-02-27T13:27:22.058] : Verifying staging area
 [2021-02-27T13:27:22.058] : ISO unmounted successfully
 [2021-02-27T13:27:22.058] : Staging process completed successfully


Command> software-packages list --staged
 [2021-02-27T13:27:29.058] :
        category: Bugfix
        kb: http://kb.vmware.com/kb/000051550
        vendor: VMware, Inc.
        name: VC-6.5.0U2-Appliance-FP
        tags: [u'']
        summary: Update for VMware vCenter Server Appliance 6.5.0
        version_supported: [u'6.5.0.5100', u'6.5.0.5200', u'6.5.0.5300', u'6.5.0.5400', u'6.5.0.5500', u'6.5.0.5600', u'6.5.0.5700', u'6.5.0.10000', u'6.5.0.10100', u'6.5.0.11000', u'6.5.0.12000', u'6.5.0.13000', u'6.5.0.14000', u'6.5.0.14100', u'6.5.0.15000']
        thirdPartyInstallation: False
        releasedate: May 03, 2018
        TPP_ISO: False
        version: 6.5.0.20000
        buildnumber: 8307201
        rebootrequired: True
        productname: VMware vCenter Server Appliance
        eulaAcceptTime: 2021-02-27 13:27:22 UTC
        severity: Critical
```
You can now see in `version_supported` that 6.5.0.15000 is listed. Install the patch:

```shell
Command> software-packages install --iso --acceptEulas
 [2021-02-27T13:28:45.058] : ISO mounted successfully
 [2021-02-27T13:28:45.058] : Staged 95 packages.
 [2021-02-27T13:28:45.058] : Verifying staging area
 [2021-02-27T13:28:45.058] : ISO unmounted successfully
 [2021-02-27T13:28:45.058] : Validating software update payload
 [2021-02-27T13:28:45.058] : Compatible patch
 [2021-02-27T13:28:45.058] : Validation successful
 [2021-02-27 13:28:45,917] : Copying software packages  [2021-02-27T13:28:45.058] : ISO mounted successfully 95/95
 [2021-02-27T13:28:58.058] : ISO unmounted successfully
 [2021-02-27 13:28:58,940] : Running test transaction ....
 [2021-02-27 13:28:59,955] : Running pre-install script.....
 [2021-02-27T13:31:12.058] : All VMware services are stopped.
 [2021-02-27 13:31:12,834] : Upgrading software packages ....
 [2021-02-27 13:32:25,223] : Running post-install script.....
 [2021-02-27T13:32:27.058] : Packages upgraded successfully, Reboot is required to complete the installation.
 ```

Rebooting the appliance with the command `shutdown reboot -r "Appliance Patch"` and it came back up with 6.5 Update 2. After that I repeated the process with the original patch file `VMware-vCenter-Server-Appliance-6.5.0.24100-13834586-patch-FP.iso`  and I got to the target version:

![VCSA 6.5U2h]({{ site.url }}/assets/images/VCSA-6.5U2h.png)

## Wrap Up

From now on I am going to use the shell for all upgrades as frankly it's easier. Also, lesson learnt - more time researching the upgrade path results in less time Googling!