---
layout: single
classes: wide
title: "Packer Build Error with VMware Workstation 16"
date: 2022-01-19
category: [VMware]
excerpt: "Packer builds were failing using VMware Workstation v16. How to fix and a potential bug in the installation process"
---
## Introduction

I recently got my Packer [vsphere-iso](https://www.packer.io/plugins/builders/vsphere/vsphere-iso) builds targetting vCenter working exactly as I wanted, but I use Workstation as my primary environment.  I started to develop builds for the [vmware-iso](https://www.packer.io/plugins/builders/vmware/iso) builder. However I hit an error when trying to generate the VMs. I did my usual research on Google but could not find the same issue detailed elsewhere. This post is written in case anyone else experiences the same issue.

This was happening on a Windows 10 PC running VMware Workstation 16.2.1. The Packer versions were:

```
packer {

  required_version = ">= 1.7.8"

  required_plugins {
    vmware = {
      version = ">= 1.0.3"
      source = "github.com/hashicorp/vmware"
    }
  }
}
```

### The Build Issue

The VM was a simple Windows Server 2022 where I translated the build from the `vsphere-iso` version to `vmware-iso`. Running `packer validate` and `packer inspect` returns no issues:

```posh
PS C:\GitHub\packer-vmware-iso\builds\win2022> packer validate .\win2022.pkr.hcl
The configuration is valid.

PS C:\GitHub\packer-vmware-iso\builds\win2022> packer inspect .\win2022.pkr.hcl
Packer Inspect: HCL2 mode
> input-variables:
> local-variables:
> builds:
  > Windows Server 2022:
    sources:
      vmware-iso.win2022dc
    provisioners:
      windows-restart
    post-processors:
      <no post-processor>
```

Running `packer build` however threw this error:

```posh
PS C:\GitHub\packer-vmware-iso\builds\win2022> packer build .\win2022.pkr.hcl
Windows Server 2022.vmware-iso.win2022dc: output will be in this color.

==> Windows Server 2022.vmware-iso.win2022dc: Retrieving ISO
==> Windows Server 2022.vmware-iso.win2022dc: Trying ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso
==> Windows Server 2022.vmware-iso.win2022dc: Trying ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso?checksum=md5%3A064b69d7e35689acf518fa8f80269dad
==> Windows Server 2022.vmware-iso.win2022dc: ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso?checksum=md5%3A064b69d7e35689acf518fa8f80269dad => C:/GitHub/packer-vmware-iso/iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso
==> Windows Server 2022.vmware-iso.win2022dc: Configuring output and export directories...
==> Windows Server 2022.vmware-iso.win2022dc: Creating floppy disk...
    Windows Server 2022.vmware-iso.win2022dc: Copying files flatly from floppy_files
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../bootfiles/win2022/datacentercore/autounattend.xml
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/install-vmtools64.ps1
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/initial-setup.ps1
    Windows Server 2022.vmware-iso.win2022dc: Done copying files from floppy_files
    Windows Server 2022.vmware-iso.win2022dc: Collecting paths from floppy_dirs
    Windows Server 2022.vmware-iso.win2022dc: Resulting paths from floppy_dirs : []
    Windows Server 2022.vmware-iso.win2022dc: Done copying paths from floppy_dirs
    Windows Server 2022.vmware-iso.win2022dc: Copying files from floppy_content
    Windows Server 2022.vmware-iso.win2022dc: Done copying files from floppy_content
==> Windows Server 2022.vmware-iso.win2022dc: Creating required virtual machine disks
==> Windows Server 2022.vmware-iso.win2022dc: Building and writing VMX file
==> Windows Server 2022.vmware-iso.win2022dc: Could not determine network mappings from files in path: C:/Program Files (x86)/VMware/VMware Workstation
==> Windows Server 2022.vmware-iso.win2022dc: Deleting output directory...
Build 'Windows Server 2022.vmware-iso.win2022dc' errored after 14 seconds 303 milliseconds: Could not determine network mappings from files in path: C:/Program Files (x86)/VMware/VMware Workstation

==> Wait completed after 14 seconds 303 milliseconds

==> Some builds didn't complete successfully and had errors:
--> Windows Server 2022.vmware-iso.win2022dc: Could not determine network mappings from files in path: C:/Program Files (x86)/VMware/VMware Workstation

==> Builds finished but no artifacts were created.
```

Googling this exact error message did not return any exact results. Down the rabbit hole I went.

### The Fix

Through some breadcrumbs from research, it led me to a particular file in the VMware installation: `netmap.conf`.  Searching for this file in the various VMware installation folders, especially the specified `C:\Program Files (x86)\VMware\VMware Workstation` resulted in no file being found. There is not much on the Internet about this file.

I thought maybe my installation that has had many upgrades over time might be the issue so I setup a VM and performed a clean install of Workstation. I still could not find the file. I eventually found out performing the following steps generated the file:

1. In Workstation go to `Edit...Virtual Network Editor`
2. Click `Change Settings`
3. Don't change anything, just click `OK`

Now if you look in `C:\ProgramData\VMware` there is a new file called `netmap.conf`.  If you look in the file it is not complicated:

```dosbatch
# This file is automatically generated.
# Hand-editing this file is not recommended.
network0.name = "Bridged"
network0.device = "vmnet0"
network1.name = "HostOnly"
network1.device = "vmnet1"
network2.name = "VMNet2"
network2.device = "vmnet2"
network3.name = "VMNet3"
network3.device = "vmnet3"
network4.name = "VMNet4"
network4.device = "vmnet4"
network5.name = "VMNet5"
network5.device = "vmnet5"
network6.name = "VMNet6"
network6.device = "vmnet6"
network7.name = "VMNet7"
network7.device = "vmnet7"
network8.name = "NAT"
network8.device = "vmnet8"
network9.name = "VMNet9"
network9.device = "vmnet9"
network10.name = "VMNet10"
network10.device = "vmnet10"
network11.name = "VMNet11"
network11.device = "vmnet11"
network12.name = "VMNet12"
network12.device = "vmnet12"
network13.name = "VMNet13"
network13.device = "vmnet13"
network14.name = "VMNet14"
network14.device = "vmnet14"
network15.name = "VMNet15"
network15.device = "vmnet15"
network16.name = "VMNet16"
network16.device = "vmnet16"
network17.name = "VMNet17"
network17.device = "vmnet17"
network18.name = "VMNet18"
network18.device = "vmnet18"
network19.name = "VMNet19"
network19.device = "vmnet19"
```

### Testing

Once the file was generated running the same build again:

```posh
PS C:\GitHub\packer-vmware-iso\builds\win2022> packer build .\win2022.pkr.hcl
Windows Server 2022.vmware-iso.win2022dc: output will be in this color.

==> Windows Server 2022.vmware-iso.win2022dc: Retrieving ISO
==> Windows Server 2022.vmware-iso.win2022dc: Trying ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso
==> Windows Server 2022.vmware-iso.win2022dc: Trying ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso?checksum=md5%3A064b69d7e35689acf518fa8f80269dad
==> Windows Server 2022.vmware-iso.win2022dc: ../../iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso?checksum=md5%3A064b69d7e35689acf518fa8f80269dad => C:/GitHub/packer-vmware-iso/iso/20348.169.210806-2348.fe_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso
==> Windows Server 2022.vmware-iso.win2022dc: Configuring output and export directories...
==> Windows Server 2022.vmware-iso.win2022dc: Creating floppy disk...
    Windows Server 2022.vmware-iso.win2022dc: Copying files flatly from floppy_files
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../bootfiles/win2022/datacentercore/autounattend.xml
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/pvscsi.cat
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/pvscsi.inf
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/pvscsi.sys
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/txtsetup.oem
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/install-vmtools64.ps1
    Windows Server 2022.vmware-iso.win2022dc: Copying file: ../../scripts/common/initial-setup.ps1
    Windows Server 2022.vmware-iso.win2022dc: Done copying files from floppy_files
    Windows Server 2022.vmware-iso.win2022dc: Collecting paths from floppy_dirs
    Windows Server 2022.vmware-iso.win2022dc: Resulting paths from floppy_dirs : []
    Windows Server 2022.vmware-iso.win2022dc: Done copying paths from floppy_dirs
    Windows Server 2022.vmware-iso.win2022dc: Copying files from floppy_content
    Windows Server 2022.vmware-iso.win2022dc: Done copying files from floppy_content
==> Windows Server 2022.vmware-iso.win2022dc: Creating required virtual machine disks
==> Windows Server 2022.vmware-iso.win2022dc: Building and writing VMX file
==> Windows Server 2022.vmware-iso.win2022dc: Starting virtual machine...
==> Windows Server 2022.vmware-iso.win2022dc: Connecting to VNC...
==> Windows Server 2022.vmware-iso.win2022dc: Waiting 3s for boot...
==> Windows Server 2022.vmware-iso.win2022dc: Typing the boot command over VNC...
==> Windows Server 2022.vmware-iso.win2022dc: Waiting for WinRM to become available...
```

I've truncated the output but this time the build proceeds as normal. Problem fixed.

### Workstation 16 Issue?

On a hunch I decided to restore the VM i installed a clean copy of Workstation back to a state before Workstation was installed. I had a copy of VMware Workstation 15.0.0. I installed this version and looked in `C:\ProgramData\VMware` and the `netmap.conf` was present. It seems that there may be a bug in v16 where this file is not generated on installation.

## Wrap Up

A simple fix to a problem there doesn't seem to be much available about. I hope anyone else having this issue finds this post and helps them out.

I will update this post if I find a reason for the file not being present in Workstation v16.
