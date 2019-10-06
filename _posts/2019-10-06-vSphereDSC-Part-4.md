---
layout: single
title: "vSphere DSC - Part 4"
date: 2019-10-06
category: [VMware]
excerpt: "What did I learn on the way and where you can learn more"
---
## Introduction

So what started as a two post series has been doubled. I could easily write four more posts showing examples.

However in this post I will detail some things I learnt while exploring the vSphere DSC resource and where you can go and learn more.

## What I learnt

This is just a general list of observations/lessons learnt/confusing things in the resource I found. Some of them may be things I just don't understand (yet!) so please correct me if I am wrong.

### Installation

Firstly installation. When you install the Module the first thing I did was check the [Documentation](https://github.com/vmware/dscr-for-vmware/tree/master/Documentation/DSCResources) pages on GitHub. I was doing a project at work that could use the resource [VMHostPowerPolicy](https://github.com/vmware/dscr-for-vmware/blob/master/Documentation/DSCResources/VMHostPowerPolicy/VMHostPowerPolicy.md) so started trying to configure a host using the examples. I could not get it to work. Eventually I realised the resource was not installed on my machine! It seems there is documentation on GitHub that are for resources not in version 2.0 of vSphere DSC.

So when you look at the Documentation pages in the list of resources the following are not actually part of the module - yet I assume:

* VDSwitch
* VMHostAdvancedSettings
* VMHostAgentVM
* VMHostCache
* VMHostGraphics
* VMHostGraphicsDevice
* VMHostPciPassthrough
* VMHostPhysicalNic
* VMHostPowerPolicy
* VMHostVssNic
* VMHostVssPortGroup
* VMHostVssPortGroupSecurity
* VMHostVssPortGroupShaping
* VMHostVssPortGroupTeaming

### Overlapping Cluster Resources

There seem to be some overlapping resources in the module. For instance in [vSphere DSC - Part 3]({{ site.baseurl }}{% post_url 2019-10-02-vSphereDSC-Part-3 %}) I showed and example using the [DrsCluster](https://github.com/vmware/dscr-for-vmware/blob/master/Documentation/DSCResources/DrsCluster/DrsCluster.md) resource. If you actually look at the [list of resources](https://github.com/vmware/dscr-for-vmware/tree/master/Documentation/DSCResources) there are three cluster related resources:

1. Cluster - The resource is used to create, update and delete Clusters in a specified Datacenter. The resource also takes care to configure Cluster's High Availability (HA) and Drs settings.
2. DrsCluster - The resource is used to create, update and delete Clusters in a specified Datacenter. The resource also takes care to configure Cluster's Drs settings.
3. HACluster - The resource is used to create, update and delete Clusters in a specified Datacenter. The resource also takes care to configure Cluster's High Availability (HA) settings.

If we look at the parameters for each resource:

| Cluster | DrsCluster | HACluster |
| --- | --- | --- |
| Server | Server | Server |
| Credential | Credential | Credential |
| Name | Name |  Name |
| Location | Location | Location |
| DatacenterName | DatacenterName | DatacenterName |
| DatacenterLocation | DatacenterLocation | DatacenterLocation |
| Ensure | Ensure | Ensure |
| HAEnabled | | HAEnabled |
| HAAdmissionControlEnabled | | HAAdmissionControlEnabled |
| HAFailoverLevel | | HAFailoverLevel |
| HAIsolationResponse | | HAIsolationResponse |
| HARestartPriority | |HARestartPriority |
| DrsEnabled | DrsEnabled | |
| DrsAutomationLevel | DrsAutomationLevel |
| DrsMigrationThreshold | DrsMigrationThreshold |
| DrsDistribution | DrsDistribution |
| MemoryLoadBalancing | MemoryLoadBalancing |
| CPUOverCommitment | CPUOverCommitment |

Basically Cluster covers DrsCluster and HACluster. Why have multiple resources? I'd just use Cluster all the time.

### Server and Name Parameters

As mentioned in Part 1:

> If we were authenticating to a vCenter we would define the vCenter in `Server` and the actual ESXi host in `Name`. As we are dealing with an ESXi host directly in this example we put the hostname in both `Server` and `Name` instead of just defining `Name` and omitting `Server`. This is the same for any of the resources that can target an ESXi host directly.

## Where you can learn more

First obvious places to read up are the VMware blog posts for the initial [v1.0 release](https://blogs.vmware.com/PowerCLI/2018/12/getting-started-dsc-for-vmware.html) and then the latest [v2.0 release](https://blogs.vmware.com/PowerCLI/2019/06/new-release-dsc-resources-for-vmware-2-0.html) of the vSphere DSC Module.

[Kyle Ruddy](https://twitter.com/kmruddy) presented at the PowerShell Conference EU in 2019 and covered in depth vSphere DSC. Thankfully the conference recorded all the videos and Kyles session is available here - [Kyle Ruddy - Manage vSphere with PowerCLI DSC Resources, Finally!](https://www.youtube.com/watch?v=8EOnyTzfTAg). As an aside the [PowerShell Conference EU videos](https://www.youtube.com/channel/UCxgrI58XiKnDDByjhRJs5fg/videos) are well worth browsing through as there is excellent content in there.

Kyle also did a Code breakout session at VMworld US this year called [How PowerCLI Makes vSphere Configuration Management Easy (CODE2214U)](https://videos.vmworld.com/global/2019?q=CODE2214U). This is an excellent session that covers:

* What is Configuration Management?
* What is PowerShell
* What is PowerCLI?
* Desired State Configuration (DSC) Introduction
* DSC Resources for VMware

Finally I will post my example DSC scripts on my GitHub on the [vSphere-DSC repository](https://github.com/cwestwater/vSphere-DSC/tree/master/Examples).

## Wrap Up

I've really enjoyed learning more about DSC in general and applying it to my first love vSphere. DSC seems to get a hard life in Configuration Management circles, but I like using it as it is easy to pick up and native to the systems I use. I'd recommend giving it a go.
