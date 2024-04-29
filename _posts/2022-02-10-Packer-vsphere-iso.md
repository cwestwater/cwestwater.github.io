---
layout: single
classes: wide
title: "Packer vsphere-iso Windows Repo"
date: 2022-02-10
category: [VMware]
excerpt: "My repo for using HashiCorp Packer to build vSphere Windows Templates"
---
## Introduction

HashiCorp Packer is an awesome tool that you can use to create templates on a variety of platforms including VMware vSphere.

This post is basically a placeholder to my GitHub Repo that has code that uses the [vsphere-iso](https://www.packer.io/plugins/builders/vsphere/vsphere-iso) builder to create a variety of Templates.

You can access the Repo at [cwestwater/packer-vsphere-iso](https://github.com/cwestwater/packer-vsphere-iso).

## HashiCorp Packer

I'm not going to re-hash plenty of great content out there about Packer. I will however give you some great resources to learn:

- [Stephan McTighe](https://twitter.com/vStephanMcTighe) did a great series of posts on [Getting Started with Packer to Create vSphere Templates](https://stephanmctighe.com/2021/06/15/getting-started-with-packer-to-create-vsphere-templates-part-1/)
- [Troy Lindsay](https://twitter.com/troylindsay42) presented a great session at VMware Code 2020 called [CODE4234: Building VM Templates Programmatically with Packer with Troy Lindsay](https://youtu.be/mO0oeCAjeO8). Great video - I learnt a LOT from this video
- [Michael Poore](https://twitter.com/mpoore) has an amazing [repo](https://github.com/v12n-io/packer) with some great builds for most common OS's
- [HashiCorp Learn](https://learn.hashicorp.com/packer) has lots of great tutorials on Packer available

## Build Details

The Packer build created the following Operating Systems:

- Windows Server 2016 Datacenter
- Windows Server 2016 Datacenter Core
- Windows Server 2016 Standard
- Windows Server 2016 Standard Core
- Windows Server 2019 Datacenter
- Windows Server 2019 Datacenter Core
- Windows Server 2019 Standard
- Windows Server 2019 Standard Core
- Windows Server 2022 Datacenter
- Windows Server 2022 Datacenter Core
- Windows Server 2022 Standard
- Windows Server 2022 Standard Core

I use the Evaluation ISOs from the [Microsoft Evaluation Center](https://www.microsoft.com/en-gb/evalcenter/evaluate-windows-server) for my lab.

The builds will create a VM that has the following specifications:

- 2 vCPUS
- 4GB RAM
- 20GB HDD
- HW version 17
- vmxnet 3 NIC
- PVSCSI Storage Controller
- VMware Tools installed automatically

Of course the specifications are easily changed by modifying the variables passed to the build.

I also perform some VM customisation as I detailed in these posts:

- [VM Console Display Resolution Change]({{ site.baseurl }}{% post_url 2018-08-19-VMConsole-Resolution-Change %})
- [Eject VM Hardware]({{ site.baseurl }}{% post_url 2021-10-20-VM-Eject-Devices %})

Once the OS is built the following OS customisations are performed:

- Disables TLS 1.0 and 1.1
- Disables a lot of services that are not needed
- Removes Features that are not required in a Server OS
- Some general OS customisations

The build then uses the amazing plugin [Packer Windows Update Provisioner](https://github.com/rgl/packer-plugin-windows-update) to update Windows with all the latest patches.

At the end of the process it converts the VM to a template.

## Wrap Up

Please go have a look at my repo [cwestwater/packer-vsphere-iso](https://github.com/cwestwater/packer-vsphere-iso) on GitHub if you want to get an easy on ramp to Infrastructure as Code. Building Templates is an easy way to get started with IaC which could then be built on with tools such as Terraform to deploy those templates.

I will be keeping the repo up to date as Packer develops and I make changes to how I build my templates. I hope it helps someone get started with Packer.
