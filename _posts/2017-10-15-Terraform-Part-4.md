---
layout: single
title: "Terraform with vSphere - Part 4"
date: 2017-10-15
category: [VMware,Terraform]
excerpt: "This is Part 4 on how to use Terraform with vSphere. Let's look at some resources for learning more"
---
# Wrap up

We have barely scratched the surface with Terraform. It is a very powerful piece of software that can do much more than building a single VM from a template. I do find the documentation around the vSphere provider to be lacking and there is not much out there on the internet on using it with vSphere, so it's been a lot of experimentation for me but very enjoyable.

I first started playing with Terraform after watching [Nick Colyer's](https://twitter.com/vnickc) excellent Pluralsight course [Automating AWS and vSphere with Terraform](https://www.pluralsight.com/courses/terraform-automating-aws-vsphere). It gives good demo driven examples similar to what I have shown in this series but goes further by delving into Modules, local provisoners, remote provisioners, etc. If you have Pluralsight access go check it out.

I also found this series of posts from [Gruntwork](https://blog.gruntwork.io/why-we-use-terraform-and-not-chef-puppet-ansible-saltstack-or-cloudformation-7989dad2865c) to be excellent. The posts have actually been converted into a book called [Terraform: Up & Running](https://www.terraformupandrunning.com/?ref=gruntwork-blog-comprehensive-terraform) so you should go check it out.

Finally I also found these posts to be helpful:

* [Martin Rusev](https://twitter.com/martin_rusev) - [Building your infrastructure with Terraform - Review and Walkthrough](https://www.amon.cx/blog/building-your-infrastructure-with-terraform/)
* [Emil Wypych](https://twitter.com/emwypych) - [Deploying vSphere VM with Terraform](https://emilwypych.com/2017/02/26/deploying-vsphere-vm-with-terraform/)

Don't forget the official documentation on [Terraform.io](https://www.terraform.io/docs/providers/vsphere/index.html) - sometimes you just have to RTFM.

As you have found Terraform is easy to pick up and you can see results very quickly. It's given me the bug to dig deeper and see what I can apply at work. Good luck in your Terraform journey!