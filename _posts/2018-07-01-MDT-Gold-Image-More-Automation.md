---
layout: single
title: "MDT Gold Image VM Automation - More Automation"
date: 2018-07-01
category: [VMware, Microsoft, MDT, PowerCLI]
excerpt: "Further automation to an earlier script for Gold Image creation in MDT"
---
# Introduction

In one of my early blog posts I wrote one called [MDT Gold Image VM Automation]({{ site.baseurl }}{% post_url 2017-02-19-MDT-Gold-Image-VM-Automation %}) that detailed how I used PowerCLI to build either a Windows 7 or 10 VM. This was used as part of MDT to build our Gold images. There was still a part that required a manual step so I decided to eliminate it.

## The Problem

The script brings up a menu where you select either Windows 7 or 10. PowerCLI then builds the VM in vCenter and powers it on. The manual part was when MDT started you still needed connect to the VM console to select the Windows 7 or 10 task sequence to actually kick off the MDT deployment. This can be solved by setting a custom MAC address on the VM then use some rules in bootstrap.ini in MDT to fire up the correct task sequence.

## Setting a MAC Address

In the script the VM was created using the PowerCLI cmdlet `New-VM`. For example to create the Windows 7 VM the script line is:

~~~ posh
New-VM -Name $Win7VM -CD -Datastore $vmDatastore -DiskGB 45 -DiskStorageFormat Thick -GuestId windows7_64Guest -MemoryGB 6 -NumCpu 2 -NetworkName $vmNetwork -VMHost $vmHost | Out-Null
~~~

After the VM is created, we need to set a defined MAC address on the NIC. This is done using the cmdlet `Set-NetworkAdapter` with the parameter `-MacAddress`. VMware have an assigned Organizational Unique Identifier of 00:50:56 as the first 3 Octects so we just need to define the last three.

To update the MAC address of the VM we need to get the VM, then the NIC, then finally set the MAC address:

~~~ posh
Get-VM $Win7VM | Get-NetworkAdapter | Set-NetworkAdapter -MacAddress 00:50:56:01:01:01 -Confirm:$false | Out-Null
~~~

For Windows 10 it will be:

~~~ posh
New-VM -Name $Win10VM -CD -Datastore $vmDatastore -DiskGB 45 -DiskStorageFormat Thick -GuestId windows8_64Guest -MemoryGB 4 -NetworkName $vmNetwork -VMHost $vmHost | Out-Null
Get-VM $Win10VM | Get-NetworkAdapter | Set-NetworkAdapter -MacAddress 00:50:56:01:01:02 -Confirm:$false | Out-Null
~~~

You can see for Windows 7 the MAC address is 00:50:56:01:01:01 and Windows 10 00:50:56:01:01:02. We will need these values for MDT.

## MDT

We can use bootstrap.ini in MDT to define the MAC addresses and then call the task sequence we want. Usually bootstrap.ini just has a `[Default]` section and it runs all the rules in this. However you can change define a MAC address, Default Gateway, machine type, etc. The new bootstrap.ini we can use is:

~~~ winbatch
[Settings]
Priority=MACAddress,Default
Properties=MyCustomProperty

[00:50:56:01:01:01]
_SMSTSOrgName=GOLD Windows 7 Image
TaskSequenceID=GOLD-W7
SkipTaskSequence=YES

[00:50:56:01:01:02]
_SMSTSOrgName=GOLD Windows 10 Image
TaskSequenceID=GOLD-W10
SkipTaskSequence=YES

[Default]
OSInstall=Y
UserDataLocation=NONE
DoCapture=YES
~~~

I have snipped the `[Default]` section.

So you can see we have set the first priority as the MAC address of the VM so it processes the entries in that section, then moves on `[Default]` section. So when MDT detects the MAC address of 00:50:56:01:01:01 is goes the the `[00:50:56:01:01:01]` section and runs the task sequence with the ID of `GOLD-W7`. If the VM running has the MAC 00:50:56:01:01:02 then it goes the `[00:50:56:01:01:02]` section and calls the `GOLD-W10` task sequence that builds the Windows 10 GOLD image.

By doing these changes when the VM boots there is no need to get into the VM console and choose a task sequence for the OS type.

## Wrap Up

I have put the full PowerCLI script up in [GitHub](https://github.com/cwestwater/MDT/blob/master/New-GOLDImageVM.ps1) if you want to look. If you want to use it in your environment just update the variables in the script and then change your bootstrap.ini as detailed above.

You may be thinking there is still a manual step in there. Once the script runs you  have to select Windows 7 or Windows 10. If you wanted to remove this the script could be modified to remove the menu part and just build the two Gold VM's. It could then be run as a scheduled task monthly to generate fresh Gold images. it all depends if you want to manually kick off the Gold Image refresh or have it done as a scheduled task.