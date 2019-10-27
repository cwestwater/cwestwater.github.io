---
layout: single
title: "VMware Fling - Virtual Machine Compute Optimizer"
date: 2019-10-28
category: [VMware]
excerpt: "An overview of a VMware Fling I recently found that can give you some great performance recommendations"
---
## Introduction

I recently found a VMware Fling that helped me find some VMs that were incorrectly configured VMs in regards to vCPU and vNUMA rightsizing. I feel Flings are not that well known so want to highlight this one and show the value in checking them out.

## What are VMware Flings?

According to the [Flings website](https://flings.vmware.com/) they are:

> Flings are apps and tools built by our engineers and community that are intended to be explored

Think of them as tools/utilities/scripts/apps that are built by VMware Engineers and the Community for the benefit of us the users. They can target a niche problem or something wider.

Disclaimer:

> **They are not officially supported by VMware**

Again:

> **They are not officially supported by VMware**

Currently on the website there are 142 Flings available. Of those 142:

* 2 are Community authored
* 9 are Open Source
* 131 are VMware authored

Now you may think you have not heard of or used a VMware Fling but you most likely have. Functionality now found in the vSphere suite like the [HMTL5 web client](https://flings.vmware.com/vsphere-html5-web-client) in vCenter, the [ESXi host client](https://flings.vmware.com/esxi-embedded-host-client), and [PowerCLI Core](https://flings.vmware.com/powercli-core) first started as Flings.

## Virtual Machine Compute Optimizer Fling

The Fling I want to go over in this post is the [Virtual Machine Compute Optimizer](https://flings.vmware.com/virtual-machine-compute-optimizer). This PowerCLI based tool looks at the environment and outputs a csv that lists every VM and if it's vCPU, Power Policy and memory configuration is optimal. These optimizations are based on published best practice from VMware.

There are two scripts and a documentation PDF as part of the Fling:

* `Virtual_Machine_Compute_Optimizer.ps1` is used to check all VMs in one or more vCenters
* `Get-OptimalvCPU.ps1` is used to check an individual VM

The excellent documentation details how to run the scripts so I will skip that part and focus on what it can tell you about your environment.

## The csv Output

The following columns are present in the csv:

* vCenter Name
* Cluster Name
* Cluster Minimum Host Memory
* Cluster Minimum CPU Sockets
* Cluster Minimum CPU Cores
* ESXi Hostname
* ESXi Version
* Host Memory
* Host Sockets
* Host Cores per Socket
* Host CPU Threads
* Host HyperThreading enabled
* Host Power Policy
* VM Name
* VM Hardware Version
* VM CPU Hot Add Enabled
* VM Memory
* VM Sockets
* VM Cores per Socket
* vCPUS (Sockets x Cores)
* VM Optimized
* Optimal Sockets
* Optimal Cores per Socket
* Priority
* Details

The last five columns are the ones that we are really interested in. When I ran the script I filtered on `VM Optimized` to see the VMs I needed to look at. Next I filtered on High priority VMs to look at.

The Details column is the column that advised what needs to be fixed. From the documentation:

| Priority | Detail | Explanation |
| --- | --- | --- |
| Low | VM does not span pNUMA nodes, but consider configuring it to match pNUMA architecture | vCPU and memory fit into 1 pNUMA node, but best practice would be to match the host architecture. |
| Medium | Host hardware in the cluster is inconsistent. Consider sizing VMs based on the minimums for the cluster | Cluster has host(s) that do match others. VM findings are still based on current host, but if VMs can vMotion across hosts, the smallest host values should be used. |
| Medium | VM vCPUs exceed the host's physical cores. Consider reducing the number of vCPUs | Although hyperthreading enabled on a host can be beneficial, a VM should not be configured with vCPUs in excess of the number of physical cores on the host. |
| Medium | Consider changing the host Power Policy to "High Performance" for clusters with VMs larger than 8 vCPUs | Hosts with large VMs or IO latency sensitive workloads can benefit from the High Performance Power Policy setting. [Performance Best Practices for VMware vSphere 6.7](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/techpaper/performance/vsphere-esxi-vcenter-server-67-performance-best-practices.pdf) |
| High | VM <CPU/Memory> spans pNUMA nodes and should be distributed evenly across as few as possible | [Virtual Machine vCPU and vNUMA Rightsizing – Rules of Thumb](https://blogs.vmware.com/performance/2017/03/virtual-machine-vcpu-and-vnuma-rightsizing-rules-of-thumb.html) |
| High | VM has an odd number of vCPUs and spans pNUMA nodes | [Virtual Machine vCPU and vNUMA Rightsizing – Rules of Thumb](https://blogs.vmware.com/performance/2017/03/virtual-machine-vcpu-and-vnuma-rightsizing-rules-of-thumb.html) |
| High | VM spans pNUMA nodes, but pNUMA is not exposed to the guest OS | The host physical NUMA will not be exposed to the guest’s vNUMA in the following cases: vHW <8, CpuHotAddEnabled = TRUE, vCPUs <9, Advanced Setting "Numa.Vcpu.Min > VM vCPUs |

Some pretty great recommendations against your VMs can be identified in a matter of seconds using the scripts. I think `Virtual_Machine_Compute_Optimizer.ps1` would be a great thing to run when you inherit an environment or you are a consultant visiting somewhere new.

When I ran it I found a VM that had more vCPUs assigned than the host had physical cores (not a great idea!) and many VMs that had vCPUs incorrectly setup for NUMA. For example a VM with 1 socket/8 cores when it would be best to be set as 2 sockets/4 cores.

## Wrap Up

I have looked at VMware Flings in the past and used some but this was a great new one to find. It can give you easily actionable recommendations to improve the performance of your VMs. Great work by the creators [Mark Achtemichuk](https://flings.vmware.com/users/mark-achtemichuk) and [Mark McGill](https://flings.vmware.com/users/mark-mcgill).

Go download it an check your environment. You may be surprised what you find.
