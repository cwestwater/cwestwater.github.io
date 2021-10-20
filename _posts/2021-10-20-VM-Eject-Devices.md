---
layout: single
classes: wide
title: "Eject VM Hardware"
date: 2021-10-20
category: [VMware]
excerpt: "By default on a Windows OS VMware VM you can eject critical hardware. How do you control that?"
---
## Introduction

During [VMworld](https://www.vmware.com/vmworld/en/index.html) I attended the session SEC1662: Protecting Workloads: Tips, Tricks and Best Practices with [Bob Plankers](https://twitter.com/plankers) and I made a suggestion on a customisation I make to my VMs. Bob thought this was a great one so I thought I'd document it in this blog post.

### Default in a Windows VM

In this example I created a Windows Server 2022 VM with default options in Workstation. When the VM boots and you look in the system tray the Safely Remove and Eject Hardware option in the System Tray shows the following:

![Default Devices]({{ site.url }}/assets/images/devices-hotplug-01.png)

Clicking down the list it will let you eject everything apart from the `VMware Virtual NVMe Disk` and the `PCI Device`.

This is not really ideal when a user of the VM can eject critical hardware such as the NIC `Intel(R) 82574 Gigabit Network Connection`. I have experienced this before when *someone* (ok me) mis-clicked and ejected the NIC. Goodbye VM connectivity.

What can be done to change this 'capability'?

### Resolution

The fix is actually very easy. In the VMX configuration file you add the following line:

``` bash
devices.hotplug = "FALSE"
```

Once the VM is started up you can now see in the OS:

![Fixed Devices]({{ site.url }}/assets/images/devices-hotplug-02.png)

Only the mounted CD is available to be be ejected now which is exactly what we want to see.

## Wrap Up

This is a simple thing to do on your VMs which I have added to my Packer builds. I'm a firm believer in hiding or removing 'features' or 'capabilities' from users/builds that don't need to be there. This is a prime example of something that is there by default that can cause issues on a VM with little effort.

Think about if you should be doing this too on your Windows builds.