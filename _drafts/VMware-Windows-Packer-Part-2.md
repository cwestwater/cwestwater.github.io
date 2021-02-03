---
layout: single
classes: wide
title: "Packer with VMware to Build Windows - Part 2"
# date: YYYY-MM-DD
category: [VMware]
excerpt: "Part 2 of a series on using HashiCorp Packer to build Windows 2019 templates on ESXi"
---
## Introduction

In [Part 1]({{ site.baseurl }}{% post_url 2017-10-08-Terraform-Part-2 %}) of this series we covered why Packer is a great tool to learn, how to install and what our goals were for this series.

In this post I will cover what we will need to do to create the Windows 2019 image and the required `autounattend.xml` file.

## What We Need to Do

There is some work to do before we start using Packer to build 2019,

All Windows installations require an `autounattend.xml`  to automate the Windows setup. For background on this refer to the [Microsoft Docs site](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/automate-windows-setup). The file we will create will perform all the steps required to silently install Windows, install VMware Tools, setup WinRM and create credentials Packer can use to connect for guest customisations.



Next we need to decide on the VM configuration. In these posts I will build the VM like this:

- 2 vCPUs
- 4GB RAM
- BIOS Firmware
- Hardware version 16
- vmxnet3 network card
- 40GB Thin provisioned disk using pvscsi controller

This is a good enough specification for a template. As
autounattend
VMware tools
Builder
Provisioner
