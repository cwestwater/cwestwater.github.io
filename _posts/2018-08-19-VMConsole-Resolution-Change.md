---
layout: single
title: "VM Console Display Resolution Change"
date: 2018-08-19
category: [VMware,PowerCLI]
excerpt: "How to stop an annoying issue of the VM's display resolution changes when using the Web Console or VMRC"
---
# Introduction

Recently I noticed that when logging into VM's at the console level through vCenter the resolution was all messed up. I set the VM's at 1280x800 but was seeing resolutions all over the place.

It turned out people were using the Virtual Machine Remote Console or Web Console from vCenter to access the VM's console and it was changing the resolution of the VM's operating system to whatever the size of the web browser window was set to.

Annoying more than anything but after a couple of times finding someone with a HD monitor in portrait resolution setting the VM to 1080x1920 I decided to see what was going on and stop it.

## VMware KB52031

I noticed this seemed to be happening since we upgraded to vCenter 6.5 so after some Googling I found the VMware KB article:

[How to disable auto-fitting of Windows guest OS screen resolution when accessing from Web Client and VMRC (52031)](https://kb.vmware.com/s/article/52031)

The symptoms fitted exactly what I was seeing and tested:

> When opening or resizing VMRC of any versions or Web Console of Web Client 6.5, the screen resolution of Windows Guest OS changes to fit the window size of the client if the guest OS is Windows. This results in changing the screen resolution of the guest.

The fix is not so good. You need to modify the vmx file of the VM when it is powered off. Not great. The settings are:

> guestInfo.svga.wddm.modeset="FALSE"
guestInfo.svga.wddm.modesetCCD="FALSE"
guestInfo.svga.wddm.modesetLegacySingle="FALSE"
guestInfo.svga.wddm.modesetLegacyMulti="FALSE"

This can be done a couple of ways but I whipped up a quick PowerCLI script to change the vmx file quickly when I have one off for regular maintenance. As this issue is just annoying I am not scheduling an outage for the VM's to make the change, but only when I have VM's that I can take a quick outage on.

## PowerCLI script

First of all power off the VM from the OS, the console, vCenter, PowerCLI or whatever way you want. Make sure the VM is powered off.

The script is pretty straightforward. First connect to vCenter:

~~~ posh
Connect-VIServer -Server vc-01.cook.local
~~~

You will be prompted for credentials to connect.

Next we will prompt for a VM name that we want to change and define it into a variable:

~~~ posh
$vm = Read-Host -Prompt 'Enter a VM name to edit'
~~~

Now the VM is off we can add the lines to the vmx file. The PowerCLI cmdlet is [`New-AdvancedSetting`](https://code.vmware.com/docs/6978/cmdlet-reference#/doc/New-AdvancedSetting.html):

~~~ posh
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modeset -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetCCD -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetLegacySingle -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetLegacyMulti -Value FALSE -Confirm:$false
~~~

We can now power on the VM:

~~~ posh
Start-VM -VM $vm
~~~

Here is the full script:

~~~ posh
Connect-VIServer -Server vc-01.cook.local
$vm = Read-Host -Prompt 'Enter a VM name to edit'
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modeset -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetCCD -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetLegacySingle -Value FALSE -Confirm:$false
Get-VM -Name $vm | New-AdvancedSetting -Name guestInfo.svga.wddm.modesetLegacyMulti -Value FALSE -Confirm:$false
Start-VM -VM $vm
~~~

## Wrap Up

Short and simple post and script to fix this 'issue'. Now the resolution of the VM's console can only be modified in the OS or through Group Policy which what I want.