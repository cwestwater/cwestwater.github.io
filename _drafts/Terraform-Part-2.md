---
layout: single
title: "Terraform with vSphere - Part 2"
# date: 2017-10-08
category: [VMware,Terraform]
excerpt: "This is Part 1 on how to use Terraform with vSphere. Let's go over Terraform basics"
---
# Introduction

In Part 1 of this series we went about installing Terraform, verifying it was working and setting up Visual Studio Code. In this part we will cover some Terraform basics.

# Terraform Components

The three Terraform Constructs we are going to look at are:

* Providers
* Resources
* Provisioners

## Providers

Providers are the resources or infrastructure we can interact with in Terraform. These can include AWS, Azure, vSphere, DNS, etc. A full list is available on the Terraform [website](https://www.terraform.io/docs/providers/index.html). As you can see it's a very big list. In this series we will concentrate on the [VMware vSphere Provider](https://www.terraform.io/docs/providers/vsphere/index.html).

## Resources

Resources are the things we are going to use in the provider. In the vSphere realm this can be a Virtual Machine, Networking, Storage, etc.

## Provisioners

Terraform uses Provisioners to talk to the back end infrastructure or services like vSphere to create your Resources. They essentially are used to execute scripts to create or destroy resources.

# Setup Terraform for vSphere
