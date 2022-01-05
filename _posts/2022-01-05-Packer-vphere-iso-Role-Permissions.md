---
layout: single
classes: wide
title: "Packer vsphere-iso Role"
date: 2022-01-05
category: [VMware]
excerpt: "What privileges are required for Packer to build in vCenter and a lesson on security/simplicity"
---
## Introduction

HashiCorp [Packer](https://www.packer.io/) is an amazing tool to help you build virtual machine templates using code. I use it to build my templates each month with the latest Windows Updates applied. I'd highly recommend looking into Packer if you maintain templates. Fellow vExpert [Stephan McTighe](https://twitter.com/vStephanMcTighe) created an excellent [blog series](https://stephanmctighe.com/2021/06/15/getting-started-with-packer-to-create-vsphere-templates-part-1/) on Packer to create vSphere Templates which I would highly recommend if you want to get started.

One of the requirements to build a template is rights into the vCenter. You could just use your normal admin credentials but I like to create an account in the vCenter with the least privileges requires narrowed down the appropriate resources in vCenter. Least privilege people! However I found an interesting problem when using Content Libraries and locking down the permissions.

This post will cover two things:

1. How to create a new role in vCenter using PowerCLI
2. Where to apply the role in vCenter

This post was written using Packer v1.7.8, vCenter Server 7.0 Update 3a and ESXi 7.0 Update 2c. The VM being created isn't really relevant, but it was Windows Server 2022 Standard.

### Required Privileges

Looking at the [vsphere-iso Builder documentation](https://www.packer.io/docs/builders/vsphere/vsphere-iso#required-vsphere-permissions) it lists the permissions required to build a VM in vCenter and where they should be applied - for example the VM Folder, the Datastore, etc.

However I find these are incomplete and too high level. For instance I use a floppy drive to apply the `autounattend.xml` for the Windows build which needs defined. There ia a link to a [GitHub Issue](https://github.com/jetbrains-infra/packer-builder-vsphere/issues/97) that details more granular permissions but I have found these to be incorrect too. I suspect this is because it's an old issue and the versions of vSphere brought in new APIs/functionality. I have found this set of privileges work for me:

- Content Library
  - Add Library Item
  - Update Library Item
- Datastore
  - Allocate Space
  - Browse Datastore
  - Low level file operations
- Network
  - Assign network
- Resource
  - Assign virtual machine to resource pool
- Virtual Machine
  - Change configuration
    - Add new disk
    - Add or remove device
    - Advanced configuration
    - Change CPU count
    - Change Memory
    - Change Settings
    - Change resource
    - Set annotation
  - Edit inventory
    - Create from existing
    - Create new
    - Remove
  - Interaction
    - Configure CD media
    - Configure floppy media
    - Connect devices
    - Inject USB HID scan codes
    - Power off
    - Power on
  - Provisioning
    - Create template from virtual machine
    - Mark as template
    - Mark as virtual machine
  - Snapshot management
    - Create snapshot

How do we create this role using PowerCLI? The cmdlet we need to use is [New-VIRole](https://developer.vmware.com/docs/powercli/latest/vmware.vimautomation.core/commands/new-virole/#Default). This cmdlet needs to be passed the name of the role and a list of privileges. To make readability easier we can add all the privileges to an array and use that in `New-VIRole`:

```posh
$PackerRolePrivileges = @(
    'ContentLibrary.AddLibraryItem',
    'ContentLibrary.UpdateLibraryItem',
    'Datastore.AllocateSpace',
    'Datastore.Browse',
    'Datastore.FileManagement',
    'Network.Assign',
    'Resource.AssignVMToPool',
    'VApp.Export',
    'VirtualMachine.Config.AddNewDisk',
    'VirtualMachine.Config.AddRemoveDevice',
    'VirtualMachine.Config.AdvancedConfig',
    'VirtualMachine.Config.Annotation',
    'VirtualMachine.Config.CPUCount',
    'VirtualMachine.Config.Memory',
    'VirtualMachine.Config.Resource',
    'VirtualMachine.Config.Settings',
    'VirtualMachine.Interact.DeviceConnection',
    'VirtualMachine.Interact.PowerOff',
    'VirtualMachine.Interact.PowerOn',
    'VirtualMachine.Interact.PutUsbScanCodes',
    'VirtualMachine.Interact.SetCDMedia',
    'VirtualMachine.Interact.SetFloppyMedia',
    'VirtualMachine.Inventory.Create',
    'VirtualMachine.Inventory.CreateFromExisting',
    'VirtualMachine.Inventory.Delete',
    'VirtualMachine.Provisioning.CreateTemplateFromVM',
    'VirtualMachine.Provisioning.MarkAsTemplate',
    'VirtualMachine.Provisioning.MarkAsVM',
    'VirtualMachine.State.CreateSnapshot'
)

$newRoleName = "Packer Build Role"

New-VIRole -Name $newRoleName -Privilege (Get-VIPrivilege -ID $PackerRolePrivileges)
```

This creates a new role called `Packer Build Role`

## Applying the Role

This is where I found things got tricky. I started applying the role to various parts of vCenter such as the Cluster, Datastores, Network, etc but it got messy fast. Eventually I stumbled upon the correct combination of places to apply the role using a specific `packer` account. Everything seemed to work well until I started using [content_library_destination](https://www.packer.io/docs/builders/vsphere/vsphere-iso#content_library_destination) which allows you to upload the template to a [Content Library](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vm_admin.doc/GUID-254B2CE8-20A8-43F0-90E8-3F6776C2C896.html).

A key thing to remember is that a Content Library is a Global Permission at the vCenter level. When you apply the Packer Role as a Global Permission it means those rights are applied at the top level object hierarchy of the vCenter and propagated down.

So this comes down to a design decision - are you using content Libraries to house your templates (and you should really)? In that case you will have to accept that the role will be available on objects that are not in the cluster you build on, the datastores you don't use, etc. However the role is very well defined. It can build a VM and manipulate it. As long as you take note or audit which accounts are using this role I am comfortable with this risk. It comes down to secrets management really.

You could lock it down to objects such as a management cluster by going to the cluster permissions, and changing the role the build service account from the `Packer Build Role` to `No Access` and propagate that down to the child objects. However Deny permissions should always be the exception. What if you decide down the line to change the cluster you build on to the denied one? Guarantee you will have forgotten about that `No Access` permission and spend a lot of time trying to figure out the issue.

This holds true if you are not using content libraries and apply the role to specific parts of your vCenter. Yes it improves security, but at what cost? More complexity, and potential to break your builds if you change a variable entry like `vm_network`.

## Wrap Up

You may have come here looking for where to apply the role to the clusters, datastores, etc. However it's a lesson. Sometimes it's not the best way to do this. A better solution is to create a well defined role (as detailed above) and only use it with a service account that has a complex password that is only used for Packer builds. That's my opinion but would love to hear yours! Reach out to me on [Twitter](https://twitter.com/cwestwater)

Remember if you are using content libraries your options are limited...
