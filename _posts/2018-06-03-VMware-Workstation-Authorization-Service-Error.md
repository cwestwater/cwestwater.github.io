---
layout: single
title: "VMware Workstation 14 - Authorization Service Error"
date: 2018-06-03
category: [VMware]
excerpt: "VMware Workstation would not start VM's due to an issue with the VMware Authorization Service"
---
# Introduction

I started VMware Workstation (Windows, v14.1.2 build-8497320) tonight and when I tried to start a VM I was getting the following error:

![Workstation Error]({{ site.url }}/assets/images/Workstation-Error.png)

Here is how I fixed it.

## Troubleshooting

The error message above is pretty self-explanatory. I checked the services:

![Workstation Error]({{ site.url }}/assets/images/Authorization-Service.png)

Sure enough it wasn't started. I tried to start it but it failed to start. So I checked the System Event Log:

~~~ winbatch
Log Name:      System
Source:        Service Control Manager
Date:          29/05/2018 19:18:43
Event ID:      7024
Task Category: None
Level:         Error
Keywords:      Classic
User:          N/A
Computer:      P500
Description:
The VMware Authorization Service service terminated with the following service-specific error: 
%%6000009
~~~

Googling the error led to a couple of links:

* [Powering on a virtual machine fails with the error: The VMware Authorization Service is not running (1007131)](https://kb.vmware.com/s/article/1007131)
* [Workstation 14 Upgrade – Authorization Service error 6000009](http://www.vbrain.info/2018/01/15/workstation-14-upgrade-authorization-service-error-6000009/)

Manfreds blog post was the closest to the error but this was not a fresh upgrade to v14 and the `vmmon` driver was at the v14 level.

After thinking about it the only thing I had done recently was to apply the Windows 10 update [KB4100403](https://support.microsoft.com/en-us/help/4100403/windows-10-update-kb4100403) which updated the OS to build `17134.81`.

## The Fix

In the end I went with the rather unsatisfactory (to me anyway) fix of running a Repair of the Workstation install and then rebooting the computer.

After that the `VMware Authorization Service` service was started Automatically and I could start VM's.

As I said I am not really satisfied with the fix as I don't understand **why** the service wouldn't start, but in the end a quick fix is sometimes the best thing.

Hoping this helps someone out there.