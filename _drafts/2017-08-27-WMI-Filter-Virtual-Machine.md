---
layout: single
title: "WMI Filter to target a Virtual machine"
date: 2018-08-27
category: [VMware, Microsoft]
excerpt: "Using a WMI filter you can easily target Virtual Machines for a GPO"
---
I was creating a GPO at work today where I had to target Virtual Machines only. We do not have VM's in their own OU, so I needed to create a WMI filter to only target VM's.

This is actually pretty simple. On a VM if open a command prompt and type the command:

~~~ batch
