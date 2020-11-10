---
layout: single
classes: wide
title: "ESXi Host SSL Certificate Trust"
date: 2020-11-10
category: [VMware]
excerpt: "How to trust the SSL certificate in for seamless browser access"
---

## Introduction

I run my lab nested in VMware Workstation but I do have a physical standalone ESXi host (a [Lenovo ThinkCenter M700 Tiny]({{ site.baseurl }}{% post_url 2019-07-21-Lenovo-ThinkCenter-M700-Tiny %})) which I use for quick testing VMs, PowerCLI, Packer, etc. It's a minor annoyance to click through the SSL Certificate prompt:

![SSL Certificate]({{ site.url }}/assets/images/ESXi-SSL-01.png)

Then when you get through to the login screen the certificate is not trusted:

![SSL Cert]({{ site.url }}/assets/images/ESXi-SSL-02.png)

This post will show you how to trust that certificate in the browser to make connections to the host that little bit more seamless.

## Enable SSH on the Host

First you will need to enable SSH on the host to regenerate the certificate then download it later on. Log into your host and go to Manage....Services...TSM-SSH then click Start (or ensure service is running):

![SSH]({{ site.url }}/assets/images/ESXi-SSL-03.png)

![SSH]({{ site.url }}/assets/images/ESXi-SSL-04.png)

## Regenerate Certificate

While we are dealing with certificates you may as well generate a fresh certificate. SSH in to the host and run the following commands:

```bash
[root@esxi2:~] cd /etc/vmware/ssl
[root@esxi2:/etc/vmware/ssl] /sbin/generate-certificates
```

then reboot the host (so pick your maintenance window):

```bash
[root@esxi2:/etc/vmware/ssl] reboot
```

We now have a fresh certificate, but it is still not trusted by your computer or browser.

## Import the Certificate

Next up it to get the correct file from the host. Use a package such as [WinSCP](https://winscp.net/eng/index.php) to copy the file off the host to your computer. The file you need to grab is `/etc/vmware/ssl/castore.pem`. Next on your workstation open an MMC console and add the Certiciates snap-in. Make sure when adding the snap-in select Computer Account:

![MMC]({{ site.url }}/assets/images/ESXi-SSL-05.png)

Expand out `Certificates...Trusted Root Certification Authorities...Certificates` then right click on `Certificates` and select `All Tasks...Import`

![MMC]({{ site.url }}/assets/images/ESXi-SSL-06.png)

Click `Next` then in the `File to Import` screen browse to the downloaded castore.pem file you downloaded earlier. You will have to change the file type drop down to `All Files (*.*)`:

![MMC]({{ site.url }}/assets/images/ESXi-SSL-07.png)

After clicking `Next` in the Certificate Store screen ensure the `Trusted Root Certification Authorities` is selected:

![MMC]({{ site.url }}/assets/images/ESXi-SSL-08.png)

Complete the wizard. Let's check the Host UI now:

![SSL Trust]({{ site.url }}/assets/images/ESXi-SSL-09.png)

## Wrap Up

When running a homelab a full on Certificate Authority is not really necessary. Trusting the certificates just makes day to day access easier.
