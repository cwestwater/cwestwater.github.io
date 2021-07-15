---
layout: single
classes: wide
title: "VMware Workstation LAN Segments"
date: 2021-07-15
category: [VMware]
excerpt: "LAN Segments are a under appreciated part of Workstation. This post will look into this useful feature"
---
## Introduction

A feature in [VMware Workstation PRO ](https://www.vmware.com/uk/products/workstation-pro.html) that I use in my lab is LAN Segments. I feel this is a under appreciated/used feature in Workstation so in this post I will cover what they are, how to use them, benefits, drawbacks, and some further information.

At the time of this post I was using [VMware Workstation 16.1.2](https://docs.vmware.com/en/VMware-Workstation-Pro/16.1.2/rn/VMware-Workstation-1612-Pro-Release-Notes.html).

### What are LAN Segments?

LAN Segments can be thought of as an isolated network environment. Any VMs in the segment can talk to each other but not to the outside world. It is like having the VMs on an isolated switch.  Only by setting up some routing appliance or VM can external access can be achieved. More on this later.

DHCP is not available in the segment either, so any VMs joined to the segment will not receive an IP address, unless you setup a DHCP server on a VM in the segment.

### How to Use LAN Segments

In Workstation you usually go to `Edit...Virtual Network Editor` to make changes to virtual networks:

![Workstation Network Settings]({{ site.url }}/assets/images/WS-Network-Settings.png)

As you can see there is no mention of LAN Segments. You actually configure LAN Segments on a VM settings dialog box:

![LAN Segments 01]({{ site.url }}/assets/images/WS-LAN-Segments-01.png)

You can see in the screenshot above I have a LAN Segment called Lab-2021. How did I create this? Click on `LAN Segments...` button to open this dialog box:

![LAN Segments 02]({{ site.url }}/assets/images/WS-LAN-Segments-02.png)

There is not much to configure here just `Add`, `Rename` and `Remove`. As stated above there is no DHCP available or any advanced settings you can get configure like in Virtual Network. I have not found a limit to the number of LAN Segments you can add. As a test I added 50 with no issue.

Once the LAN Segment is created, to add a VM select an LAN Segment from the drop down and click `OK`:

![LAN Segments 03]({{ site.url }}/assets/images/WS-LAN-Segments-03.png)

That VM is now isolated with any other VMs in that LAN Segment.

### How I use LAN Segments

So what are the benefits of LAN Segments? I like them to isolate my Lab environments. As you can see in the screenshot above I have two Labs. I refresh it every year so create a new LAN Segment and build the new lab in it. This ensures I am keeping the lab in their own space and can easily clean up the LAN Segment when I decommission the lab.

As the LAN Segment has no outside access and I need internet access in the lab, how do I accomplish this? I will refer back to a post I wrote some time ago - [pfSense in VMware Workstation]({{ site.baseurl }}{% post_url 2018-06-10-pfSense-VMware-Workstation %}). In the pfSense VM there are two interfaces:

![LAN Segments 04]({{ site.url }}/assets/images/WS-LAN-Segments-04.png)

You can see the first interface is assigned to the NAT Virtual Network which provides access to the network my PC is using, hence internet access. The second interface is on the LAN Segment. DHCP in the LAB has the IP Address on the LAN Segment interface as the gateway so the VMs can get out of the LAN Segment to the internet.

I also create a LAN segment if I am creating something quick and easy for testing and then I can be sure it won't interfere with anything else on the network or in Workstation.

### Benefits and Drawbacks of LAN Segments

I've gone over much of this in the post but to summarize the benefits of LAN Segments:

- Provides a nice isolated network environment
- Over 20 available LAN Segments (then again if you have over 20 Virtual Networks you may want to rethink your layout)
- Easy to identify the network you are using
- This is a tenuous one but if you are using Workstation on a computer you do not have admin rights on you can create/modify/delete LAN Segments without Admin rights. To edit Virtual Networks you do require Admin rights

The drawbacks are:

- No DHCP so you either need to static IP everything or setup a DHCP server
- To get outside access from the LAN Segment you need an additional VM to act as a router

### Wrap Up

I really like the usefulness of LAN Segments. They help me manage my lab in a secure manner and are simple to use. I recommend you see how you can use them too in your lab.

The VMware Documentation link for LAN segments is available [here](https://docs.vmware.com/en/VMware-Workstation-Pro/16.0/com.vmware.ws.using.doc/GUID-DEE1E2F1-5DA4-4C83-B7C5-A1165C84C757.html).