---
layout: single
classes: wide
title: "Elgato Stream Desk and VMware Workstation"
date: 2021-04-26
category: [VMware]
excerpt: "Using the Stream Deck to start VMs "
---
## Introduction

I have been hearing more about [Elgato Stream Decks](https://www.elgato.com/en/stream-deck) and started to research them. I always thought they were for streamers and could only be used for software like [OBS](https://obsproject.com/) but upon research there is great uses cases for them outside of the original purpose.

My home lab is based on nested VMs in VMware Workstation. As I don't want to leave my computer on 24/7 I power off the VMs when not in use. Each time I want to start the lab I need to power on the pfsense router, the domain controller, vCenter, ESXi hosts, a Windows 10 VM, etc. Yes it is easy just to press play on each one, but what is the fun in that?

Using Stream Deck am able to now press a single button which starts Workstation and starts up the VMs in the correct order with a pause between power on actions.

The steps below were performed on Windows 10 running VMware Workstation 16.1.1 and Elgato Stream Deck v4.9.4.13228.

## What is a Stream Deck?

The Stream Deck is essentially a USB connected keyboard that can run macros. What is cool about it is that each button is a mini display. You can assign actions to each button and the icon can reflect and change depending on that action.  This is controlled by software running on the PC.  Out of the box the software has options for common streaming functions but you can download plenty of other plugins to integrate with other software.

Elgato have some useful videos available on YouTube. Click the image to view on YouTube:

[![Introduction to Elgato Stream Deck](http://img.youtube.com/vi/IOAAFIMLFk8/0.jpg)](http://www.youtube.com/watch?v=IOAAFIMLFk8)

[![5 Ways to Use Stream Deck in the Workplace](http://img.youtube.com/vi/uX9XnnHK80M/0.jpg)](http://www.youtube.com/watch?v=uX9XnnHK80M)

There are lots of third party plugins available that can easily be installed in the Stream Deck software to extend functionality. Some highlights for me are:

- [Zoom Plugin](https://lostdomain.org/stream-deck-plugin-for-zoom/) by [Martjin Smit](https://twitter.com/smitmartijn). This lets you control Zoom meetings
- [Audio Switcher](https://github.com/fredemmott/StreamDeck-AudioSwitcher) by Fred Emmott. This lets you easily switch between audio devices on the computer. I use this to switch between my various output devices easily
- Nanoleaf Controller so I can shutdown my daughters lights on command
- [Stream Deck for Visual Studio Code](https://github.com/nicollasricas/streamdeckvsc) which lets you run commands in Visual Studio Code

## VMware Workstation

As we can run macros using Stream Deck how does that work with VMware Workstation? There are two options available:

- The main `vmware.exe`  can be used at the command line with various switches
- An executable called `vmrun.exe` which is part of the VMware VIX API

Which one to choose? `vmrun.exe` can be used against not only Workstation, but also ESXi and Fusion but `vmware.exe` is 'native' to Workstation. While I was researching for this setup,  the main Workstation documentation site still refers to a [PDF](http://www.vmware.com/resources/compatibility/search.php?deviceCategory=software) on using `vmrun.exe`. The PDF references Workstation 9 and the last revision was in 2012. It still works, but I decided to focus on `vmware.exe` to control my VMs.

The documentation for this can be found at [vmware Command Options](https://docs.vmware.com/en/VMware-Workstation-Pro/16.0/com.vmware.ws.using.doc/GUID-7369457F-FE1D-40FE-97B6-B29CA4916CCD.html).

The `vmware.exe` can be found in the main installation folder for Workstation: `C:\Program Files (x86)\VMware\VMware Workstation`. The particular command line options we are going to use to start a VM are:

| Option | Description |
| --- | --- |
| -t | Opens a virtual machine in a new tab |
| -x | Powers on the virtual machine when Workstation starts |
| path_to_vm.vmx | Path to the VM vmx file |

An example of using the command to start a VM called `ROUTER` stored in `D:\Lab-2021\router` would be:

``` dos
"C:\Program Files (x86)\VMware\VMware Workstation\vmware.exe" -t -x D:\Lab-2021\router\router.vmx
```

Now we know how to start a VM from the command line we can use this in the Stream Deck  software.

## Stream Deck Setup

Open the Stream Deck software and search for `Multi Action` and add it to a free button:

![Stream Deck]({{ site.url }}/assets/images/stream-deck-01.png)

Click on the `Title` and give it a name. In this case `Start Lab`. Next click the arrow beside `actions`:

![Stream Deck]({{ site.url }}/assets/images/stream-deck-02.png)

Now multiple actions can be added to the open box. Search for `Open` which comes up under System. This action allows you to run any command you would normally be able to start at the command line. In this case we will call `vmware.exe` with the command line options needed to launch the VM. The first one I start is the [pfSense]({{ site.baseurl }}{% post_url 2018-06-10-pfSense-VMware-Workstation %}) router.

Drag the `Open` actions in the main box. Give it a name such as the VM name. Then in the `App / File:` enter the appropriate command which in this case is `"C:\Program Files (x86)\VMware\VMware Workstation\vmware.exe" -t -x D:\Lab-2021\router\router.vmx`:

![Stream Deck]({{ site.url }}/assets/images/stream-deck-03.png)

Once that VM starts I add a delay to give it a head start before the next VM boots. This time search for an action called `Delay`. Drag this under the action to start the first VM. You can then set a delay in ms. In this example it's 10000ms which is 10 seconds:

![Stream Deck]({{ site.url }}/assets/images/stream-deck-04.png)

You can then simply repeat those two steps to add another VM to start, add a delay and chain up as many times as you want to start as many VMs in Workstation as you need. I have 6 VMs that now start by pressing the single button on the Stream Deck.

## Wrap Up

The Stream Desk is a very interesting product to help with you home setup and home lab. I am looking forward to seeing what else I can use it for to help with my productivity.
