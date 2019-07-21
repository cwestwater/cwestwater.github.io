---
layout: single
title: "Lenovo ThinkCenter M700 Tiny"
date: 2019-07-21
category: [VMware]
excerpt: "I picked up a bargain and put ESXi 6.7 on it for the homelab. Is it worth it?"
---
# Introduction

I was walking past a Cash Converters the other day and something in the window caught my eye. It was a [Lenovo ThinkCenter M700 Tiny](https://www.lenovo.com/gb/en/desktops-and-all-in-ones/thinkcentre/m-series-tiny/M700-Tiny/p/11TC1MTM700) for £199. After looking at the specs I picked it up.

This is a review of the hardware and what I have done with it.

## Specs

The model I picked up is specifically part number `10J0S68M0B`. This has the following in it:

* Intel Core i5-6500T with 4 cores at 2.5GHz
* 8GB RAM
* 120GB SSD
* 1 x 1 Gigabit Ethernet Port
* Intel Integrated Graphics
* Dual DisplayPort
* 6 x USB 3.0 Ports
* 1 M.2 SSD Slot (Empty)
* Windows 10 Pro

There was even 255 days of warranty left on it when I checked online.

What interested me was the potential for upgrades and if it would run ESXi.

## Upgrades

I ordered a 16GB SODIMM from Amazon and a put it in the spare slot to give me 24GB RAM. It will go up to 32GB RAM when I buy another.

As it has an empty M.2 slot I plan to buy a second SSD for the device. I have two reasons for this. The supplied SSD is only 128GB so pretty small. Secondly if I buy it I could potentially use the M700 as a vSAN node.

I could add another Gigabit Ethernet using a USB 3.0 adapter to provide some additional bandwidth and redundancy.

Lastly if I wanted to go all out the system will support up to a 6th Generation i7 CPU but for my workloads the i5-6500T is sufficient.

## ESXi 6.7

I downloaded and installed the vanilla ESXi 6.7 ISO from the VMware website. As it's not a server or even supported on the [Hardware Compatibility List](https://www.vmware.com/resources/compatibility/search.php) I didn't try the Lenovo customised image that is available.

I installed ESXi to a spare Kingston 16GB USB flash drive and it installed with no issues. All hardware components were picked up with no additional VIBs needed.

![ESXi1]({{ site.url }}/assets/images/esxi1.png)

As you can see from the screenshot everything looks good.

## What To Do With It?

So I run my lab nested on VMware Workstation 15 but it runs on a big Workstation which I don't want to leave on 24/7 for power usage. Core networking services is handled by Unifi and Pi-hole for DNS.

So what will I do with the M700? At work we run a very locked down network so I can't access my lab at home. However 443 is open so I want to see if I can spin up a some SSH or RDP Gateway solution to give me access. Also sometimes I want to see if I can migrate my Unifi Video NVR to this host using a container.

I am keeping my eye out for another M700 (or two) and try to run a small vSAN cluster. I do this already nested but I still love physical hardware.

## Wrap Up

I think I got a bit of a bargain with this M700 Tiny. The favourite devices seem to be NUCs for running labs on but this is pretty much a comparable device for a lot less money. I'm pretty happy and hope to pick up one or two more if I can find any on eBay or the shop I found it in.
