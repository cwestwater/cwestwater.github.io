---
layout: single
title: "Terraform with vSphere - Part 1"
date: 2017-10-01
category: [VMware,Terraform]
excerpt: "This is Part 1 on how to use Terraform with vSphere. Let's get Terraform setup"
---
# Introduction

Terraform is one of the new products that let you treat infrastructure as Code. What does Infrastructure as Code actually mean?

According to [Wikipedia](https://en.wikipedia.org/wiki/Infrastructure_as_Code):
>Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools.

In the case of Terraform this means using code to *declare* what we want from vSphere, AWS, Azure, OpenStack etc. and then Terraform goes and creates the infrastructure to our declared final state. This is the opposite to Procedural Infrastructure where we have to describe *how* to get our end result. Terraform does the hard work in figuring out how to create the infrastructure we have defined - we don't have to worry how to actually create it or the sequence of steps to get there.

Terraform uses Providers to interface with the infrastructure or service you want to work with. Examples are AWS, Azure, GCP, vSphere, OpenStack, etc. It can also work with things like DNS, Chef, GitHub, and Kubernetes. There is a full list of providers [here](https://www.terraform.io/docs/providers/index.html). I am going to use the [VMware vSphere provider](https://www.terraform.io/docs/providers/vsphere/index.html) in this series of posts. I will be using Windows and vSphere 6 throughout.

# Installing

Terraform is incredibly simple to install. Grab the download from:

https://www.terraform.io/downloads.html

It's downloaded as a Zip file with a single file - terraform.exe. I create a folder called `C:\Terraform` and place the file in there. That's it! I would also recommend you add `C:\Terraform` to your Path in the Windows Environment variables.

Terraform is also available using [Chocolatey](https://chocolatey.org/packages/terraform) if you prefer. If you have Chocolatey setup you can install Terrform with the command:

~~~ posh
choco install terraform
~~~

# Verify Installation

To verify Terraform is setup (which is pretty hard to screw up!) open a command prompt. If you have not added the path to your environment variables browse to `C:\Terrform` and run the command:

~~~ winbatch
C:\Terraform>terrform -version
Terraform v0.10.6
~~~

# Visual Studio Code Setup

Now of course you are using [Visual Studio Code](https://code.visualstudio.com/) right? I have moved on from Notepad to NotePad++ to the PowerShell ISE to VS Code. It's such a great product to use. One of the things that make it great is the extensions you can install to help with code syntax. Of course there is one for Terraform. From the extension page the Features at a glance are:

* Syntax highlighting for .tf and .tfvars files (and .hcl)
* Automatic format on save using terraform fmt
* Automatically closes braces and quotes
* Adds a command for running terraform validate
* Linting support with the help of tflint
* EXPERIMENTAL:
  * Browse document symbols
  * Browse workspace symbols
  * Peek definition
  * Goto definition
  * Find references
  * Completion for variables and outputs
  * Rename variables (and all usages)

You can install this extension in the usual ways. Either click the Install button on the page or in VS Code go to the extensions area and search for Terraform.

# Wrap up

So that's it, we have Terraform and Visual Studio code ready to start working. In Part 2 we will begin with some Terraform fundamentals.