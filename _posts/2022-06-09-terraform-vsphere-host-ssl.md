---
layout: single
classes: wide
title: "Add ESXi Host to Cluster with Terraform"
date: 2022-06-09
category: [VMware,Terraform]
excerpt: "How to add a host to a cluster in Terraform when using self-signed, untrusted certificates, a common home lab situation"
---
## Introduction

In the past I have used [HashiCorp Terraform](https://www.terraform.io/) to deploy VMs but wanted to see if I could deploy my vCenter and Hosts in my lab using Terraform. First up was to create a Cluster and add some hosts. However I hit a problem with SSL certs on the hosts and thus could not add them to the vCenter. After piecing together some documentation and some Googling I cracked it.

This post is to document my issue and help someone else trying to do this.

## Terraform Versions and vSphere

In this blog post I am using:

- [Terraform v1.2.2](https://www.terraform.io/downloads)
- [vsphere provider v2.1.1](https://registry.terraform.io/providers/hashicorp/vsphere/2.1.1)

```posh
PS C:\repos\terraform-vsphere> terraform --version
Terraform v1.2.2
on windows_amd64
+ provider registry.terraform.io/hashicorp/vsphere v2.1.1
```

I have the following setup in vCenter:

- Datacenter called `contoso`
- Cluster called `prod_cluster`

I wish to add a host to the cluster called `esxi01.ad.contoso.com`. As this is a lab environment there are just the default certificates in vSphere being used.

## Terraform Configuration

The [vsphere provider](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs) is able to add a host using [vsphere_host resource](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/resources/host). My code to add the host first looked as so:

```terraform
terraform {
  required_version = ">=1.2.2"

  required_providers {
    vsphere = {
      source  = "hashicorp/vsphere"
      version = "~> 2.1.1"
    }
  }
}

provider "vsphere" {
  user           = "administrator@vsphere.local"
  password       = "VMware1!"
  vsphere_server = "vcsa01.ad.contoso.com"

  # If you have a self-signed cert
  allow_unverified_ssl = true
}

#### Variables ####
variable "datacenter_name" {
  description = "(Required) The name of the datacenter to create"
  type        = string
  default     = "contoso"
}

variable "prod_cluster_name" {
  description = "(Required) The name of the Production datacenter to create"
  type = string
  default = "prod_cluster"
}

#### Data Collection ####
data "vsphere_datacenter" "contoso_datacenter" {
  name = var.datacenter_name
}

data "vsphere_compute_cluster" "prod_cluster" {
  name = var.prod_cluster_name
  datacenter_id = data.vsphere_datacenter.contoso_datacenter.id
}

#### Add Host to Cluster ####
resource vsphere_host "esxi01" {
  hostname = "esxi01.ad.contoso.com"
  username = "root"
  password = "VMware1!"
  cluster = data.vsphere_compute_cluster.prod_cluster.id
}
```

Lets break it down. First of all in the `terraform` block I am defining the version of Terraform I want to use and the version of the vSphere provider. Next is the `provider "vsphere"` block. Here is where the connection to the vCenter is defined.

Next is a couple of variables that define the datacenter and cluster names as detailed above. For readability I am just using the default values defined for the variables.

In the `Data Collection` section I am getting some data values. First is [data "vsphere_datacenter"](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/data-sources/datacenter) which is used to get the datacenter id, which is then used in the [data "vsphere_compute_cluster"](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/data-sources/compute_cluster) to get the cluster id. The cluster id is needed when adding the host to the cluster.

Finally the host is added to the cluster using [resource vsphere_host](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/resources/host).

Next is to run Terraform Plan:

```posh
PS C:\repos\terraform-vsphere> terraform plan -out main.tfplan
data.vsphere_datacenter.contoso_datacenter: Reading...
data.vsphere_datacenter.contoso_datacenter: Read complete after 0s [id=datacenter-4029]
data.vsphere_compute_cluster.prod_cluster: Reading...
data.vsphere_compute_cluster.prod_cluster: Read complete after 0s [id=domain-c5003]
vsphere_host.esxi01: Refreshing state... [id=host-6005]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # vsphere_host.esxi01 will be created
  + resource "vsphere_host" "esxi01" {
      + cluster     = "domain-c5003"
      + connected   = true
      + force       = false
      + hostname    = "esxi01.ad.contoso.com"
      + id          = (known after apply)
      + lockdown    = "disabled"
      + maintenance = false
      + password    = (sensitive value)
      + username    = "root"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Saved the plan to: main.tfplan

To perform exactly these actions, run the following command to apply:
    terraform apply "main.tfplan"
```

At this point it looks good, so time to apply:

```posh
PS C:\repos\terraform-vsphere> terraform apply main.tfplan
vsphere_host.esxi01: Creating...
╷
│ Error: host addition failed. Authenticity of the host's SSL certificate is not verified.
│
│   with vsphere_host.esxi01,
│   on main.tf line 74, in resource "vsphere_host" "esxi01":
│   74: resource vsphere_host "esxi01" {
```

The host fails to be added due to SSL certs. As I said above this is a lab, no certs are applied from a PKI.

### Thumbprint

Looking at the [resource vsphere_host](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/resources/host) there is an arguement reference called [thumbprint](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/resources/host#thumbprint):

> thumbprint - (Optional) Host's certificate SHA-1 thumbprint. If not set the CA that signed the host's certificate should be trusted. If the CA is not trusted and no thumbprint is set then the operation will fail.

This makes sense in what Terraform is reporting when running apply. So where can we get the Thumbprint? Opening the Host UI and looking at the Certificate we can see the Thumbprint:

![esxi Thumbprint]({{ site.url }}/assets/images/esxi-thumbprint.png)

So Iets try using that Thumbprint. The resource block now looks like this:

```terraform
resource vsphere_host "esxi01" {
  hostname = "esxi01.ad.contoso.com"
  username = "root"
  password = "VMware1!"
  thumbprint = "b5f14d337588feb97cfbde58ff4bab9eccd307db"
  cluster = data.vsphere_compute_cluster.prod_cluster.id
}
```

Unfortunately applying results in the same error.  I went back to the documentation and eventually stumbled upon the data source [vsphere_host_thumbprint](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/data-sources/host_thumbprint):

> The vsphere_thumbprint data source can be used to discover the host thumbprint of an ESXi host. This can be used when adding the vsphere_host resource. If the host is using a certificate chain, the first one returned will be used to generate the thumbprint.

This looks like what we need. So there is an additional data source now in the terraform file:

```terraform
data "vsphere_host_thumbprint" "thumbprint" {
  address = "esxi01.ad.contoso.com"
  insecure = true
}
```

At this point running plan shows this additonal line:

```posh
data.vsphere_host_thumbprint.thumbprint: Read complete after 0s [id=B5:F1:4D:33:75:88:FE:B9:7C:FB:DE:58:FF:4B:AB:9E:CC:D3:07:DB]
```

This is the Thumbprint we saw in the certificate above, just in a different format. So I then added this Thumbprint to the resource block:

```terraform
resource vsphere_host "esxi01" {
  hostname = "esxi01.ad.contoso.com"
  username = "root"
  password = "VMware1!"
  thumbprint = "B5:F1:4D:33:75:88:FE:B9:7C:FB:DE:58:FF:4B:AB:9E:CC:D3:07:DB"
  cluster = data.vsphere_compute_cluster.prod_cluster.id
}
```

Now running a plan looks good:

```posh
PS C:\repos\terraform-vsphere> terraform plan -out main.tfplan
data.vsphere_datacenter.contoso_datacenter: Reading...
data.vsphere_host_thumbprint.thumbprint: Reading...
data.vsphere_host_thumbprint.thumbprint: Read complete after 0s [id=B5:F1:4D:33:75:88:FE:B9:7C:FB:DE:58:FF:4B:AB:9E:CC:D3:07:DB]
data.vsphere_datacenter.contoso_datacenter: Read complete after 0s [id=datacenter-4029]
data.vsphere_compute_cluster.prod_cluster: Reading...
data.vsphere_compute_cluster.prod_cluster: Read complete after 0s [id=domain-c5003]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # vsphere_host.esxi01 will be created
  + resource "vsphere_host" "esxi01" {
      + cluster     = "domain-c5003"
      + connected   = true
      + force       = false
      + hostname    = "esxi01.ad.contoso.com"
      + id          = (known after apply)
      + lockdown    = "disabled"
      + maintenance = false
      + password    = (sensitive value)
      + thumbprint  = "B5:F1:4D:33:75:88:FE:B9:7C:FB:DE:58:FF:4B:AB:9E:CC:D3:07:DB"
      + username    = "root"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Saved the plan to: main.tfplan

To perform exactly these actions, run the following command to apply:
    terraform apply "main.tfplan"
```

Crunch time, run Terraform apply:

```posh
PS C:\repos\terraform-vsphere> terraform apply main.tfplan
vsphere_host.esxi01: Creating...
vsphere_host.esxi01: Creation complete after 7s [id=host-7001]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***Success!*** The host has been added to the cluster.

Now obviously it's not the best way to apply the host Thumbprint by hard coding the value, so it should be replaced by the data value:

```terraform
resource vsphere_host "esxi01" {
  hostname = "esxi01.ad.contoso.com"
  username = "root"
  password = "VMware1!"
  thumbprint = data.vsphere_host_thumbprint.thumbprint.id
  cluster = data.vsphere_compute_cluster.prod_cluster.id
}
```

## Wrap Up

I spent way too long trying to figure this out by not making the link between [vsphere_host](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/resources/host) and [vsphere_host_thumbprint](https://registry.terraform.io/providers/hashicorp/vsphere/latest/docs/data-sources/host_thumbprint) in the documentation. I think I'm going to look into if HashiCorp take documentation updates via GitHub and see if I can suggest an update to the docs.

Hope this help someone else out there.
