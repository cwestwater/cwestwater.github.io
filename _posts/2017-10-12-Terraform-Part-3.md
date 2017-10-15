---
layout: single
title: "Terraform with vSphere - Part 3"
date: 2017-10-12
category: [VMware,Terraform]
excerpt: "This is Part 3 on how to use Terraform with vSphere. Let's look at using Variables with Terraform"
---
# Introduction
In [Part 1]({{ site.baseurl }}{% post_url 2017-10-01-Terraform-Part-1 %}) and [Part 2]({{ site.baseurl }}{% post_url 2017-10-08-Terraform-Part-2 %}) we downloaded, setup, and then created a simple VM using Terraform. Let's look how to use variables and the files required for this.

# Existing Code

Let's look at the code from Part 2:

~~~ ruby
provider "vsphere" {
    user = "username@corp.contoso.com" # You need to use this format, not example\username
    password = "Password1"
    vsphere_server = "vcenter.corp.contoso.com"

    # If you use self-signed certificates
    allow_unverified_ssl = true
}

resource "vsphere_virtual_machine" "webserver" {
    name = "webserver1"
    vcpu = 1
    memory = 2048

network_interface {
    label = "VM Network"
}

disk {
   datastore = "datastore"
   template = "MicroCore-Linux"
}

}
~~~

We have hard coded the login details and vCenter name. These are great candidates to convert to variables. We do this by replacing them with a ```"${var.variablename}"```:

~~~ ruby
provider "vsphere" {
    user = "${var.username}" # You need to use this format, not example\username
    password = "${var.password}"
    vsphere_server = "${var.vcenter}"

    # If you use self-signed certificates
    allow_unverified_ssl = true
}
~~~

So our three variables are ```username```, ```password``` and ```vcenter```.

# Variable File

We now need to create a new file in ```C:\Terraform``` called, for example, vars.tf.  This file defines our variables:

~~~ ruby
variable "username" {}
variable "password" {}
variable "vcenter" {}
~~~

You can enter a description if you like such as:

~~~ ruby
variable "username" {
    description = "Enter the username for vCenter"
}
~~~

# Terraform Plan

By default, Terraform will look for any file that has variables defined and use them. Notice we have not actually defined our values for username, password or the vCenter, so when you run Terraform Plan you will be prompted for the details:

~~~ ruby
C:\Terraform>terraform plan
var.username
  Enter a value: username@corp.contoso.com

var.password
  Enter a value: Password

var.vcenter
  Enter a value: vcenter.corp.contoso.com

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
~~~

I've snipped the above. You can see we were prompted for the details. A step backwards you would rightly think!

# terraform.tfvars File

We should place our actual variable values in a file called ```terraform.tfvars```. If a file called exactly that is in the same folder as our code file and the variables file, Terraform will auto load the ```terraform.tfvars``` file and use the value in it.

So for our example, ```terraform.tfvars``` will look like:

~~~ ruby
username = "username@corp.contoso.com"
password = "Password1"
vcenter = "vcenter.corp.contoso.com"
~~~

Now if you run Terraform it will look at the main code file, see we have variables, look in the other vars.tf file for the definition of them and finally automatically look in terraform.tfvars

# terraform.tfvars and Source Control

Now as I am sure you are storing your Terraform files in source control such as GitHub, but think about ```terraform.tfvars```. That's your precious credentials. You don't want them up on Github available for anyone to see.

**If you are using GitHub I would highly recommend you add ```terraform.tfvars``` to your ```.gitignore``` file. This will make GitHub ignore the file and not upload it to the web.**

# More with Variables

Of course I have done something simple here and just changed the connection information to vSphere into variables. You can do much more such as making the datastore name we are using, the VM name, the template, etc. into variables. Have a look at the [documentation](https://www.terraform.io/intro/getting-started/variables.html) for more information. I have given you the building blocks, you can decide how to use them.

# Part 4
In the wrap up post I will point you in the direction of some great resources for learning more about Terraform.