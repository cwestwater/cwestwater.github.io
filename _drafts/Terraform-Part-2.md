---
layout: single
title: "Terraform with vSphere - Part 2"
# date: 2017-10-08
category: [VMware,Terraform]
excerpt: "This is Part 2 on how to use Terraform with vSphere. Let's create a VM"
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

The first thing we need to do is specify the vSphere provider and use it to connect to vCenter. This is pretty easy:

~~~ ruby
provider "vsphere" {
    user = "username@corp.contoso.com" # You need to use this format, not example\username
    password = "Password"
    vsphere_server = "vcenter.corp.contoso.com"

    # If you use self-signed certificates
    allow_unverified_ssl = true
}
~~~

As you can see above we are telling Terraform we are using vSphere and we pass the username, password and vCenter address. Note the username must be the full UPN not the short version. Also note if you are using self-signed certificates you have to allow Terraform the use them

# Create a VM

Now lets create a VM. First of all we want to tell Terraform we are creating a resource, in this case the VM. We also have to give the VM a name. We will use webserver in this example:

~~~ ruby
resource "vsphere_virtual_machine" "webserver" {
}
~~~

The code is pretty easy. You are defining a vSphere Virtual machine resource called webserver. Now we want to describe the specification of the server. Firstly use the the following required arguments for the resource:

* name - what the VM will be called
* vcpu - the number of vCPU's to give the VM
* memory - the amount of RAM the VM has

So now in the resource we can add:

~~~ ruby
resource "vsphere_virtual_machine" "webserver" {
    name = "webserver1"
    vcpu = 1
    memory = 2048
}
~~~

Now we have to define the virtual network we are going to connect to. This is required and the only required argument is the name of the network:

~~~ ruby
network_interface {
    label = "VM Network"
}
~~~

Finally we are required to define the disk. You can either specify an existing template, or in this case a new VMDK. The required arguments in this case are:

* name - the path and name of the new VMDK
* size - size of disk in GB

Even though it's an optional argument datastore is a good one to define. If you have more than one datastore and don't define then apply the code, Terraform will error:

~~~ ruby
Error applying plan:

1 error(s) occurred:

* vsphere_virtual_machine.Terraform-Web: 1 error(s) occurred:

* vsphere_virtual_machine.Terraform-Web: default datastore resolves to multiple instances, please specify
~~~

So we will define the datastore:

~~~ ruby
disk {
   name = "/webserver1/disk1.vmdk"
   size = 10
   datastore = "datastore"
}
~~~

So lets put it all together. Observe the network and disk definitions need to go in the resource code block - they are not separate resources:

~~~ ruby
resource "vsphere_virtual_machine" "webserver" {
    name = "webserver1"
    vcpu = 1
    memory = 2048

network_interface {
    label = "VM Network"
}

disk {
   name = "/webserver1/disk1.vmdk"
   size = 10
   datastore = "datastore"
}

}
~~~

# Terraform Plan

We can now check your code is correct by running Terraform Plan. [Terraform Plan](https://www.terraform.io/docs/commands/plan.html) shows what will happen if you apply the code without actually doing it. I think of this like the -Whatif switch in PowerShell. So if you run the plan for the code above you get:

~~~
C:\Terraform>terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + vsphere_virtual_machine.webserver
      id:                                     <computed>
      detach_unknown_disks_on_delete:         "false"
      disk.#:                                 "1"
      disk.3339386647.bootable:               ""
      disk.3339386647.controller_type:        "scsi"
      disk.3339386647.datastore:              "datastore"
      disk.3339386647.iops:                   ""
      disk.3339386647.keep_on_remove:         ""
      disk.3339386647.key:                    <computed>
      disk.3339386647.name:                   "/webserver1/disk1.vmdk"
      disk.3339386647.size:                   "10"
      disk.3339386647.template:               ""
      disk.3339386647.type:                   "eager_zeroed"
      disk.3339386647.uuid:                   <computed>
      disk.3339386647.vmdk:                   ""
      domain:                                 "vsphere.local"
      enable_disk_uuid:                       "false"
      linked_clone:                           "false"
      memory:                                 "2048"
      memory_reservation:                     "0"
      moid:                                   <computed>
      name:                                   "webserver1"
      network_interface.#:                    "1"
      network_interface.0.ip_address:         <computed>
      network_interface.0.ipv4_address:       <computed>
      network_interface.0.ipv4_gateway:       <computed>
      network_interface.0.ipv4_prefix_length: <computed>
      network_interface.0.ipv6_address:       <computed>
      network_interface.0.ipv6_gateway:       <computed>
      network_interface.0.ipv6_prefix_length: <computed>
      network_interface.0.key:                <computed>
      network_interface.0.label:              "VM Network"
      network_interface.0.mac_address:        <computed>
      network_interface.0.subnet_mask:        <computed>
      power_state:                            "poweredOn"
      skip_customization:                     "false"
      time_zone:                              "Etc/UTC"
      uuid:                                   <computed>
      vcpu:                                   "1"
      wait_for_guest_net:                     "true"


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
~~~

You can see the specification of the VM and the last line tells us we will add one VM to vSphere. If it all looks good let's create the VM

# Terraform Apply

So we have defined what we want, tested it using plan, and now we want to actually create the VM. We do this using [Terraform Apply](https://www.terraform.io/docs/commands/apply.html). This applies the Terraform code:

~~~
C:\Terraform>terraform apply
vsphere_virtual_machine.webserver: Creating...
  detach_unknown_disks_on_delete:         "" => "false"
  disk.#:                                 "" => "1"
  disk.3339386647.bootable:               "" => ""
  disk.3339386647.controller_type:        "" => "scsi"
  disk.3339386647.datastore:              "" => "datastore"
  disk.3339386647.iops:                   "" => ""
  disk.3339386647.keep_on_remove:         "" => ""
  disk.3339386647.key:                    "" => "<computed>"
  disk.3339386647.name:                   "" => "/Terraform/linux.vmdk"
  disk.3339386647.size:                   "" => "1"
  disk.3339386647.template:               "" => ""
  disk.3339386647.type:                   "" => "eager_zeroed"
  disk.3339386647.uuid:                   "" => "<computed>"
  disk.3339386647.vmdk:                   "" => ""
  domain:                                 "" => "vsphere.local"
  enable_disk_uuid:                       "" => "false"
  linked_clone:                           "" => "false"
  memory:                                 "" => "2048"
  memory_reservation:                     "" => "0"
  moid:                                   "" => "<computed>"
  name:                                   "" => "webserver1"
  network_interface.#:                    "" => "1"
  network_interface.0.ip_address:         "" => "<computed>"
  network_interface.0.ipv4_address:       "" => "<computed>"
  network_interface.0.ipv4_gateway:       "" => "<computed>"
  network_interface.0.ipv4_prefix_length: "" => "<computed>"
  network_interface.0.ipv6_address:       "" => "<computed>"
  network_interface.0.ipv6_gateway:       "" => "<computed>"
  network_interface.0.ipv6_prefix_length: "" => "<computed>"
  network_interface.0.key:                "" => "<computed>"
  network_interface.0.label:              "" => "Internal-to-ESXi"
  network_interface.0.mac_address:        "" => "<computed>"
  network_interface.0.subnet_mask:        "" => "<computed>"
  power_state:                            "" => "poweredOn"
  skip_customization:                     "" => "false"
  time_zone:                              "" => "Etc/UTC"
  uuid:                                   "" => "<computed>"
  vcpu:                                   "" => "1"
  wait_for_guest_net:                     "" => "true"
vsphere_virtual_machine.webserver: Creation complete after 44s (ID: webserver1)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
~~~

If you check vCenter your first VM created using Terraform should be there! Let's now clean up and delete the VM.

# Terraform Destroy

So in this simple case we created a VM. As you probably don't want it hanging about while I work on Part 3 of this series lets clean it up. You do this using [Terraform Destroy](https://www.terraform.io/docs/commands/destory.html). Be wary of using this command. I will undo/delete/destroy all the things you have created using Terraform. In this case we only created one VM so that's all we will destroy. Again **be careful** in production using this!

~~~
C:\Terraform>terraform destroy
vsphere_virtual_machine.webserver: Refreshing state... (ID: webserver1)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - vsphere_virtual_machine.webserver


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

vsphere_virtual_machine.webserver: Destroying... (ID: webserver1)
vsphere_virtual_machine.webserver: Destruction complete after 5s

Destroy complete! Resources: 1 destroyed.
~~~

And our VM has been deleted in vCenter. You will be prompted to type ```yes``` to confirm you actually want to run the command.

# Part 3
In Part 3 we will look at using variable in the code and look at some of the files Terraform uses.