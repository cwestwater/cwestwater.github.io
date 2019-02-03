---
layout: single
title: "PowerCLI Cookbook for vSAN"
date: 2019-02-03
category: [VMware]
excerpt: "VMware released a new resource for using PowerCLI with vSAN. Here is an overview and where to get it"
---
# Introduction

On the 29th January 2019 VMware, and specifically [Jase McCarty](https://twitter.com/jasemccarty), released v1.0 of the PowerCLI Cookbook for vSAN. This excellent resource provides a lot of practical advice in using PowerCLI to configure, operate and report on a vSAN implementation. The announcement blog post can he found here on [blogs.vmware.com](https://blogs.vmware.com/virtualblocks/2019/01/29/powercli-cookbook-for-vsan/).

## The Cookbook

The Cookbook pdf is available here on [storagehub.vmware.com](https://storagehub.vmware.com/section-assets/powercli-cookbook-for-vsan) (direct pdf link) and comes in at 88 pages. The main sections are:

1. Introduction
2. Expectations
3. Getting Started
4. Configuration Recipes
5. Operational Recipes
6. Reporting Recipes
7. A Summary
8. References

I really like the Getting Started section. It is great reading for anyone new to PowerCLI and PowerShell. It covers some basic topics such as PowerShell v PowerShell Core, Editors, IDEs, installation, etc. This provides anyone new to scripting a level set to start using the examples in the Cookbook.

The Configuration Recipes section covers topics such as enabling vSAN, vSAN networking, Encryption, HA, DRS, and others. Jase doesn't just dive into a bunch of PowerCLI scripts. There are screenshots of the GUI and some clear explanations which then show you the PowerCLI commands to perform the same steps.

The Operational Recipes section covers Host Maintenance & Tasks, vSAN Storage Policies and vSAN Encryption Operations. The script samples start simple, for example putting a host in maintenance mode:

~~~ posh
PS /> Set-VMHost -VMHost "sc1.scdemo.local" -State "Maintenance"
~~~

leading up to complex tasks like creating vSAN Storage Policies:

~~~ posh
PS /> New-SpbmStoragePolicy -Name "RAID1 Mirroring" -Description "Mirroring Policy" -AnyOfRuleSets (New-SpbmRuleSet(New-SpbmRule -Capability (Get-SpbmCapability -Name "VSAN.hostFailuresToTolerate") -Value 1),
(New-SpbmRule -Capability (Get-SpbmCapability -Name "VSAN.replicaPreference") -Value "RAID-1 (Mirroring) - Performance"))
~~~

Lastly the Reporting Recipes section gives some ideas for simple PowerCLI scripts to gather some basic information such as Disk Utilization, but goes on to give examples of more complex scripts that you can use as a basis to create your own reporting scripts.

What I really like about each section is that it starts simple and leads you onto more complex scripts, with excellent, clear, and descriptive explanations on what is happening. Too often Cookbooks are a one liner description of the script then a block of code. This is a refreshing change.

In my opinion this Cookbook is not just helpful for people maintaining vSAN, but anyone new to PowerCLI will appreciate the content. The Getting Started section can be used by a person new to PowerCLI. I am starting to 'force' myself to use PowerShell and PowerCLI for day to day administrative tasks instead of the GUI and some of the samples are ones I will use as we have our first couple of four node vSAN clusters. For someone totally new the explanations give good context to what is happening instead of just reading the cmdlet documentation.

If you want to learn more about the Cookbook direct from the author, Jase appeared in Episode 26 of the [Gigacast](https://twitter.com/vgigacast) talking about the cookbook. You can watch the episode on [Youtube](https://www.youtube.com/watch?v=Nv4PnJttl1M) or listen to the [Podcast](http://gigabrit.com/podlove/file/69/s/download/c/select-show/Episode - 26.mp3). As an aside the Gigacast is an engaging listen and is an essential listen on my podcast list. Keep it up [Britton](https://twitter.com/vcixnv) and [Tony](https://twitter.com/importcarguy)!

## Wrap Up

As I said in the introduction this is v1.0 of the Cookbook. I intend to check back on it and see the updates that are sure to come. One suggestion for v1.1 could be in the Getting Started section is [updating to the latest version of PowerCLI]({{ site.baseurl }}{% post_url 2018-03-11-PowerCLI-v10 %}).

The [PowerCLI Cookbook for vSAN](https://storagehub.vmware.com/section-assets/powercli-cookbook-for-vsan) should be essential reading for all vSAN administrators and anyone with an interest in PowerCLI. Brilliant work Jase and thanks for all the hard work that must have gone into this.