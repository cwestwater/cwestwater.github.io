---
layout: single
title: "ESXi Host Network Lost"
# date: YYYY-MM-DD
category: [VMware]
excerpt: "Network missing from ESXi host after upgrade"
---
# Introduction

I recently upgraded a host from v5.0 to v6.0 using `esxcli` and after the reboot I could not get connectivity to it. Once I got access to the console via iLO I saw this:

![ESXi Network Error]({{ site.url }}/assets/images/ESXiNetworkError-01.png)

Notice the usual message with the IP Address of the hsot was missing! This is what I did to troubleshoot and fix the issue.

## 