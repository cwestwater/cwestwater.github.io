---
layout: single
classes: wide
title: "Packer with VMware to Build Windows - Part 1"
# date: YYYY-MM-DD
category: [VMware]
excerpt: "Part 1 of a series on using HashiCorp Packer to build Windows 2019 templates on ESXi"
---
## Introduction

I spend time maintaining my templates in my lab which is frankly a pain in the rear. Manually installing the OS, patching, then my customisations takes time. There must be a better way. Enter [HashiCorp Packer](https://www.packer.io/).

Packer automates the creation of machine images. Think of a template in vSphere, Azure VM images, Docker images, local VirtualBox templates, etc. Instead of manually creating the image/template you code a configuration file using either JSON or HashiCorp Configuration Language (HCL) which defines how you want the image to be created. In the case of vSphere it boots an ISO, installs the OS, does in guest customisations then converts to a template.

## Benefits of Packer

Before Packer I could spend hours manually creating my images, now with Packer I run a single command and 40 minutes later I have a Windows 2019 template built. Manual builds mean manual errors typically. Even with a checklist things will not be 100% the same each time.

Packer is a great way to get into Infrastructure as Code. It's free, easy to install and can act as a building block for further tools such as [HashiCorp Terraform](https://www.terraform.io/), CI/CD Pipelines, Cloud, etc. As you will find out you can build Windows, Linux or Docket on cloud or local tools such as VMware Workstation. No matter what OS you like you can learn how to build with Packer.

Packer can be used to build on many platforms:

- Alicloud ECS
- Amazon EC2
- Azure
- Cloudstack
- DigitalOcean
- Docker
- Google Cloud
- Hetzner Cloud
- HyperOne
- Hyper-V
- Linode
- LXC
- LXD
- NAVER Cloud
- 1&1
- OpenStack
- Oracle
- Outscale
- Parallels
- ProfitBricks
- Proxmox
- QEMU
- Scaleway
- Tencent Cloud
- JDCloud
- Triton
- UCloud
- Vagrant
- VirtualBox
- VMware
- Yandex Cloud

There are some platforms in there I have never even heard of in that list.

## What Will I Cover

 As I am a VMware/Windows guy I will focus on building a Windows 2019 template using the Microsoft Evaluation download on a standalone ESXi host. There are lots of examples on the internet of building against a full vSphere environment with vCenter. However there are many homelabbers that will be building against the free standalone ESXi and I couldn't find that many examples of that. This series of posts will let anyone try out Packer without having to pay for any software.

## Required Downloads

There are a number of things you will need to download to follow along:

- [HashiCorp Packer](https://www.packer.io/downloads) - these posts were written using v1.6.6
- [VMware ESXi](https://www.vmware.com/go/get-free-esxi) - these posts were written using ESXi v7.0
- [VMware Tools](https://packages.vmware.com/tools/releases/latest/) - these posts were written using Tools v11.2.1
- [Microsoft Windows Server 2019](https://www.microsoft.com/en-gb/evalcenter/evaluate-windows-server-2019) - 180 Day free Evaluation
- [Windows Assessment and Deployment Kit](https://docs.microsoft.com/en-us/windows-hardware/get-started/adk-install)(ADK) - these posts were written using v2004
- [Microsoft Visual Studio Code](https://code.visualstudio.com/) or your IDE of choice

I'm going to assume you know how to install ESXi on some suitable hardware, you can install Windows ADK and Visual Studio Code on your computer.

Installing Packer is very simple. Download the zip file and extract `packer.exe` to a suitable folder such as `C:\HashiCorp`. Then add an environment variable to point to this location. If you need further instructions use this [link](https://learn.hashicorp.com/tutorials/packer/getting-started-install).

Check Packer is installed. Open a command prompt and navigate to the folder the `packer.exe` is in. Then run:

``` shell
C:\Users\cwestwater>cd C:\Hashicorp

C:\Hashicorp>packer --version
1.6.6
```

This confirms Packer v1.6.6 is available for use. As you setup an environment variable `packer.exe` can be run from any folder.

## Packer Terminology

The key terminology you need to understand for Packer are:

- `Artifacts` - this is the output from Packer. In our case this will be the files that make up a VMware VM
- `Builds` - this is the task that actually produces the image on the platform
- `Builders` - this is the component of Packer that reads  in your configuration in JSON or HCL and converts it to the commands that the platform you are targeting understands
- `Post Processors` - once an image is built post-processors can do tasks with the artifact
- `Provisioners` - these are the components that can do in guest configuration after the OS is installed

## Wrap Up

That enough dry material for now. In the next post I will outline  the steps on building the Windows 2019 template and create some of the files we will need for the build.