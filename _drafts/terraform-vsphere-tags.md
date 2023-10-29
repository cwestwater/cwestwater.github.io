---
layout: single
classes: wide
title: "vSphere Tags using Terraform"
# date: YYYY-MM-DD
category: [VMware,Terraform]
excerpt: ""
---
## Introduction

I have been working a great deal lately with [HashiCorp Terraform](https://www.terraform.io/), vSphere and the [vsphere provider](https://registry.terraform.io/providers/hashicorp/vsphere/latest) to build VMs. This is the first of a series of posts on using the provider.

I know some of this is covered in the documentation, but I found some of it lacking in examples and covering some more advanced ways of writing the Terraform instead of simple examples.

In this post I will cover creating Tags in vCenter. I will cover a simple one HCL file example, and a more advanced example where the code will be broken down into various files.

I will assume you know how [vSphere Tags](https://docs.vmware.com/en/VMware-vSphere/8.0/vsphere-vcenter-esxi-management/GUID-16422FF7-235B-4A44-92E2-532F6AED0923.html) work.

## Versions

This post was written using [Terraform v1.6.2](https://releases.hashicorp.com/terraform/1.6.2/) and [vSphere Provider v2.5.1](https://registry.terraform.io/providers/hashicorp/vsphere/2.5.1). I am targeting vCenter Server 8.0 Update 2.

## Simple Example

First of all we need some setup. We define the terraform and provider versions, and the credentials to connect to vCenter:

```hcl
terraform {

  required_version = ">= 1.6.2"

  required_providers {
    vsphere = {
      source  = "hashicorp/vsphere"
      version = "2.5.1"
    }
  }
}

provider "vsphere" {
  user                 = "administrator@vsphere.local"
  password             = "VMware1!"
  vsphere_server       = "vcsa-01.localdomain"
  allow_unverified_ssl = true
}
```

Standard stuff. Next we need to create a [Tag Category](https://registry.terraform.io/providers/hashicorp/vsphere/2.5.1/docs/resources/tag_category). For this example I am creating a category called Environment:

```hcl
resource "vsphere_tag_category" "environment" {
  name        = "Environment"
  description = "What environment is the VM part of"
  cardinality = "SINGLE"

  associable_types = [
    "VirtualMachine",
  ]
}
```

I am creating a resource of type `vsphere_tag_category` called `environment`. The name of the category is `Environment` and I give the category a description that will be shown in vCenter. I then define that only one tag from this category to an object, and the only object type it can be applied to is a VM.

Lastly I want to create the tags that will be under the category Environment. This is done using a resource type of [vsphere_tag](https://registry.terraform.io/providers/hashicorp/vsphere/2.5.1/docs/resources/tag):

```hcl
resource "vsphere_tag" "prod" {
  name        = "PROD"
  category_id = vsphere_tag_category.environment.id
  description = "Production VM"
}

resource "vsphere_tag" "test" {
  name        = "TEST"
  category_id = vsphere_tag_category.environment.id
  description = "Test VM"
}

resource "vsphere_tag" "dev" {
  name        = "DEV"
  category_id = vsphere_tag_category.environment.id
  description = "Development VM"
}
```

I am creating three resources of type `vsphere_tag` called `prod`, `test` and `dev` and I associate them to the `vsphere_tag_category` I created earlier. Lastly I give a description of the tag that will be shown in vCenter.

### Advanced Example



## Wrap Up
