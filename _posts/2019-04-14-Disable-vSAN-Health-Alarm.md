---
layout: single
title: "Disable vSAN Health Alarm"
date: 2019-04-14
category: [VMware]
excerpt: "How to Disable a vSAN Health Alarm using the VCSA RVC"
---
# Introduction

I am currently rebuilding my lab as it was time to refresh it. I run it all nested on [VMware Workstation 15](https://www.vmware.com/uk/products/workstation-pro.html) as I have a powerful enough PC to run a nested three host cluster easily. Deploying the ESXi and VCSA OVAs is very easy in v15 so a single PC has more WAF than a bunch of server class machines.

As part of the lab I run nested vSAN which obviously is not on the [vSAN HCL](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=vsan) so when the lab was built vSAN was showing a Health warning `SCSI controller is VMware Certified` which left an Alarm on the cluster which tweaked my OCD.

![vSAN Health Warning]({{ site.url }}/assets/images/vSAN-Health-Warning.png)

As it's a nested lab it would never pass this health check so I wanted to disable it. Using the Ruby vSphere Console (RVC) it is possible to disable any of the health checks. In this blog I will go through the procedure for cleaning up this warning in my cluster.

This is all based on vSAN version 6.6.1. In my vCenter the Datacenter is called `Contoso` and the Cluster `Corp`.

## Ruby vSphere Console

The RVC is a command line tool in the VCSA that is another CLI tool for managing vSphere. RVC is one of the main tools for managing and troubleshooting a vSAN environment.

There are a few guides on using the RVC but one I have used is a series of posts on the vSphere blog written by [Joe Cook](https://twitter.com/CloudAnimal):

* [Part 1 - Introduction to the Ruby vSphere Console](https://blogs.vmware.com/vsphere/2014/07/managing-vsan-ruby-vsphere-console.html)
* [Part 2 - Navigating your vSphere and Virtual SAN infrastructure with RVC](https://blogs.vmware.com/vsphere/2014/08/managing-virtual-san-rvc-part-2-navigating-vsphere-virtual-san-infrastructure-rvc.html)
* [Part 3 – RVC Usage and Command Syntax](https://blogs.vmware.com/vsphere/2014/08/blog3-managing-virtual-san-rvc-part-3-rvc-usage-command-syntax.html)

It's a few years old but a useful starting point for the RVC. Joe along with [Cormac Hogan](https://twitter.com/CormacJHogan) also produced the [VMware Ruby vSphere Console Command Reference for Virtual SAN](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/vsan/vmware-ruby-vsphere-console-command-reference-for-virtual-san.pdf). Again it's a few years old but a useful resource.

To log into the RVC ssh into the VCSA and type `rvc`. You will be prompted for credentials which relies on the PSC SSO for validation. In this case I am going to use the `administrator@vsphere.local` username and as I am connected to the VCSA I want to work on I specify the `localhost`:

~~~ bash
login as: root
Pre-authentication banner message from server:
|
| VMware vCenter Server Appliance 6.5.0.20000
|
| Type: vCenter Server with an embedded Platform Services Controller
|
End of banner message from server
Keyboard-interactive authentication prompts from server:
| Password:
End of keyboard-interactive prompts from server
Last login: Sat Apr 13 21:09:53 2019 from 10.10.1.11
Connected to service

    * List APIs: "help api list"
    * List Plugins: "help pi list"
    * Launch BASH: "shell"

Command> srvc
Install the "ffi" gem for better tab completion.
Host to connect to (user@host): administrator@vsphere.local@localhost
password:
0 /
1 localhost/
>
~~~

We are now in the RVC.

## RVC Navigation

In RVC vSphere and vSAN are navigated like a file system using `ls` and `cd` commands. Think of this like PowerShell. In PowerShell you can navigate the Registry like a file system. In RVC vSphere is shown in the vCenter hierarchy:

~~~ bash
> ls
0 /
1 localhost/
> cd localhost/
/localhost> ls
0 Contoso (datacenter)
/localhost> cd Contoso
/localhost/Contoso> cd /
> ls
0 /
1 localhost/
> cd localhost
/localhost> ls
0 Contoso (datacenter)
/localhost> cd Contoso
/localhost/Contoso> ls
0 storage/
1 computers [host]/
2 networks [network]/
3 datastores [datastore]/
4 vms [vm]/
~~~

You can then browse down into things like the Cluster, vDSs, vSAN, etc.

In this example I browse down to a host and into the vSAN datastore on that host using the usual `cd` and `ls` commands:

~~~ bash
> ls
0 /
1 localhost/
> cd localhost
/localhost> ls
0 Contoso (datacenter)
/localhost> cd Contoso
/localhost/Contoso> ls
0 storage/
1 computers [host]/
2 networks [network]/
3 datastores [datastore]/
4 vms [vm]/
/localhost/Contoso> cd computers
/localhost/Contoso/computers> ls
0 Corp (cluster): cpu 10 GHz, memory 7 GB
/localhost/Contoso/computers> cd Corp
/localhost/Contoso/computers/Corp> ls
0 hosts/
1 resourcePool [Resources]: cpu 10.60/10.60/normal, mem 7.07/7.07/normal
/localhost/Contoso/computers/Corp> cd hosts
/localhost/Contoso/computers/Corp/hosts> ls
0 esxi1.corp.contoso.com (host): cpu 2*1*3.50 GHz, memory 10.00 GB
1 esxi2.corp.contoso.com (host): cpu 2*1*3.50 GHz, memory 10.00 GB
2 esxi3.corp.contoso.com (host): cpu 2*1*3.50 GHz, memory 10.00 GB
/localhost/Contoso/computers/Corp/hosts> cd esxi1.corp.contoso.com
/localhost/Contoso/computers/Corp/hosts/esxi1.corp.contoso.com> ls
0 vms/
1 datastores/
2 networks/
/localhost/Contoso/computers/Corp/hosts/esxi1.corp.contoso.com> cd datastores
/localhost/Contoso/computers/Corp/hosts/esxi1.corp.contoso.com/datastores> ls
0 vsanDatastore: 96.61GB 2.0%
/localhost/Contoso/computers/Corp/hosts/esxi1.corp.contoso.com/datastores> cd vsanDatastore
/localhost/Contoso/computers/Corp/hosts/esxi1.corp.contoso.com/datastores/vsanDatastore> ls
0 files
1 vms/
2 hosts/
3 capabilitysets/
~~~

To speed up navigation when you type `ls` there is a number beside each entry. You can simply type `cd` and the number. For example:

~~~ bash
/localhost/Contoso> ls
0 storage/
1 computers [host]/
2 networks [network]/
3 datastores [datastore]/
4 vms [vm]/
/localhost/Contoso> cd 1
/localhost/Contoso/computers>
~~~

## Disabling the vSAN Health Alarm

vSAN Health Checks are performed at the Cluster level which in RVC is at `/localhost/Contoso/computers/Corp`. We can check the Cluster vSAN health by running the command `vsan.health.health_summary` with the cluster appended:

~~~ bash
> vsan.health.health_summary /localhost/Contoso/computers/Corp
Overall health: red (vSAN HCL warning)
+-----------------------------------------------------------------+---------+
| Health check                                                    | Result  |
+-----------------------------------------------------------------+---------+
| Online health (Last check: 30 minute(s) ago)                    | Passed  |
|   Customer experience improvement program (CEIP)                | Passed  |
|   Online health connectivity                                    | Passed  |
|   Physical network adapter link speed consistency               | Passed  |
|   Disks usage on storage controller                             | Passed  |
|   vSAN Critical Alert - Patch available for critical vSAN issue | Passed  |
|   vSAN max component size                                       | Passed  |
|   vCenter Server up to date                                     | Passed  |
|   Thick-provisioned VMs on vSAN                                 | Passed  |
+-----------------------------------------------------------------+---------+
| Hardware compatibility                                          | Warning |
|   vSAN HCL DB up-to-date                                        | Passed  |
|   vSAN HCL DB Auto Update                                       | Passed  |
|   SCSI controller is VMware certified                           | Warning |
|   Controller is VMware certified for ESXi release               | Passed  |
|   Controller driver is VMware certified                         | Passed  |
|   Controller firmware is VMware certified                       | Passed  |
|   Controller disk group mode is VMware certified                | Passed  |
+-----------------------------------------------------------------+---------+
| Network                                                         | Passed  |
|   Hosts disconnected from VC                                    | Passed  |
|   Hosts with connectivity issues                                | Passed  |
|   vSAN cluster partition                                        | Passed  |
|   All hosts have a vSAN vmknic configured                       | Passed  |
|   All hosts have matching subnets                               | Passed  |
|   vSAN: Basic (unicast) connectivity check                      | Passed  |
|   vSAN: MTU check (ping with large packet size)                 | Passed  |
|   vMotion: Basic (unicast) connectivity check                   | Passed  |
|   vMotion: MTU check (ping with large packet size)              | Passed  |
|   Network latency check                                         | Passed  |
+-----------------------------------------------------------------+---------+
| Physical disk                                                   | Passed  |
|   Operation health                                              | Passed  |
|   Disk capacity                                                 | Passed  |
|   Congestion                                                    | Passed  |
|   Component limit health                                        | Passed  |
|   Component metadata health                                     | Passed  |
|   Memory pools (heaps)                                          | Passed  |
|   Memory pools (slabs)                                          | Passed  |
+-----------------------------------------------------------------+---------+
| Data                                                            | Passed  |
|   vSAN object health                                            | Passed  |
+-----------------------------------------------------------------+---------+
| Cluster                                                         | Passed  |
|   ESXi vSAN Health service installation                         | Passed  |
|   vSAN Health Service up-to-date                                | Passed  |
|   Advanced vSAN configuration in sync                           | Passed  |
|   vSAN CLOMD liveness                                           | Passed  |
|   vSAN Disk Balance                                             | Passed  |
|   Resync operations throttling                                  | Passed  |
|   vCenter state is authoritative                                | Passed  |
|   vSAN cluster configuration consistency                        | Passed  |
|   Time is synchronized across hosts and VC                      | Passed  |
|   vSphere cluster members match vSAN cluster members            | Passed  |
|   Software version compatibility                                | Passed  |
|   Disk format version                                           | Passed  |
+-----------------------------------------------------------------+---------+
| Limits                                                          | Passed  |
|   Current cluster situation                                     | Passed  |
|   After 1 additional host failure                               | Passed  |
|   Host component limit                                          | Passed  |
+-----------------------------------------------------------------+---------+
| Performance service                                             | Passed  |
|   Stats DB object                                               | Passed  |
|   Stats master election                                         | Passed  |
|   Performance data collection                                   | Passed  |
|   All hosts contributing stats                                  | Passed  |
|   Stats DB object conflicts                                     | Passed  |
+-----------------------------------------------------------------+---------+
| vSAN Build Recommendation                                       | Passed  |
|   vSAN Build Recommendation Engine Health                       | Passed  |
|   vSAN build recommendation                                     | Passed  |
+-----------------------------------------------------------------+---------+

Details about any failed test below ...
Hardware compatibility - SCSI controller is VMware certified: yellow
  +------------------------+--------+------------------------------------+---------------------+----------------------+
  | Host                   | Device | Current controller                 | PCI ID              | Controller certified |
  +------------------------+--------+------------------------------------+---------------------+----------------------+
  | esxi3.corp.contoso.com | vmhba0 | VMware Inc. PVSCSI SCSI Controller | 15ad,07c0,15ad,07c0 | Warning              |
  | esxi1.corp.contoso.com | vmhba0 | VMware Inc. PVSCSI SCSI Controller | 15ad,07c0,15ad,07c0 | Warning              |
  | esxi2.corp.contoso.com | vmhba0 | VMware Inc. PVSCSI SCSI Controller | 15ad,07c0,15ad,07c0 | Warning              |
  +------------------------+--------+------------------------------------+---------------------+----------------------+

[[0.047431826, "initial connect"],
 [3.244764935, "cluster-health"],
 [0.014735734, "table-render"]]
~~~

You can see the warning under the Hardware Compatibility section.

Each health check item has a particular ID associated with it which is then used with the `vsan.health.silent_check_configure` command to disable it.

First to get the ID of the check to disable use the command `vsan.health.silent_health_check_status`:

~~~ bash
> vsan.health.silent_health_check_status /localhost/Contoso/computers/Corp
Silent Status of Cluster Corp:
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Health Check                                                                                | Health Check Id                     | Silent Status |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Cloud Health                                                                                |                                     |               |
|   Advanced vSAN configuration supported                                                     | unsupportedadvparameters            | Normal        |
|   Controller utility is installed on host                                                   | vendortoolpresence                  | Normal        |
|   Controller with pass-through and RAID disks                                               | mixedmode                           | Normal        |
|   Coredump partition size check                                                             | coredumpartitionsize                | Normal        |
|   Customer experience improvement program (CEIP)                                            | vsancloudhealthceipexception        | Normal        |
|   Disks usage on storage controller                                                         | diskusage                           | Normal        |
|   Dual encryption applied to VMs on vSAN                                                    | dualencryption                      | Normal        |
|   Network adapter is VMware certified                                                       | pnichcl                             | Normal        |
|   Online health connectivity                                                                | vsancloudhealthconnectionexception  | Normal        |
|   Patch available for critical vSAN issue for All-Flash clusters with deduplication enabled | patchalert                          | Normal        |
|   Physical network adapter link speed consistency                                           | pnicconsistent                      | Normal        |
|   RAID controller configuration                                                             | controllercacheconfig               | Normal        |
|   Thick-provisioned VMs on vSAN                                                             | thickprovision                      | Normal        |
|   vCenter Server up to date                                                                 | vcuptodate                          | Normal        |
|   vSAN Critical Alert - Patch available for critical vSAN issue                             | lilypatchalert                      | Normal        |
|   vSAN Critical Alert - vSAN Advanced Configuration Check                                   | lilyadvparametercheck               | Normal        |
|   vSAN and VMFS datastores on a Dell H730 controller with the lsi_mr3 driver                | mixedmodeh730                       | Normal        |
|   vSAN configuration for LSI-3108 based controller                                          | h730                                | Normal        |
|   vSAN max component size                                                                   | smalldiskstest                      | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Cluster                                                                                     |                                     |               |
|   Advanced vSAN configuration in sync                                                       | advcfgsync                          | Normal        |
|   Deduplication and compression configuration consistency                                   | physdiskdedupconfig                 | Normal        |
|   Deduplication and compression usage health                                                | physdiskdedupusage                  | Normal        |
|   Disk format version                                                                       | upgradelowerhosts                   | Normal        |
|   ESXi vSAN Health service installation                                                     | healtheaminstall                    | Normal        |
|   Host Maintenance Mode                                                                     | mmdecominsync                       | Normal        |
|   Resync operations throttling                                                              | resynclimit                         | Normal        |
|   Software version compatibility                                                            | upgradesoftware                     | Normal        |
|   Time is synchronized across hosts and VC                                                  | timedrift                           | Normal        |
|   vCenter state is authoritative                                                            | vcauthoritative                     | Normal        |
|   vSAN CLOMD liveness                                                                       | clomdliveness                       | Normal        |
|   vSAN Disk Balance                                                                         | diskbalance                         | Normal        |
|   vSAN Health Service up-to-date                                                            | healthversion                       | Normal        |
|   vSAN cluster configuration consistency                                                    | consistentconfig                    | Normal        |
|   vSphere cluster members match vSAN cluster members                                        | clustermembership                   | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Data                                                                                        |                                     |               |
|   vSAN VM health                                                                            | vmhealth                            | Normal        |
|   vSAN object health                                                                        | objecthealth                        | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Encryption                                                                                  |                                     |               |
|   CPU AES-NI is enabled on hosts                                                            | hostcpuaesni                        | Normal        |
|   vCenter and all hosts are connected to Key Management Servers                             | kmsconnection                       | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Hardware compatibility                                                                      |                                     |               |
|   Controller disk group mode is VMware certified                                            | controllerdiskmode                  | Normal        |
|   Controller driver is VMware certified                                                     | controllerdriver                    | Normal        |
|   Controller firmware is VMware certified                                                   | controllerfirmware                  | Normal        |
|   Controller is VMware certified for ESXi release                                           | controllerreleasesupport            | Normal        |
|   Host issues retrieving hardware info                                                      | hclhostbadstate                     | Normal        |
|   SCSI controller is VMware certified                                                       | controlleronhcl                     | Normal        |
|   vSAN HCL DB Auto Update                                                                   | autohclupdate                       | Normal        |
|   vSAN HCL DB up-to-date                                                                    | hcldbuptodate                       | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Limits                                                                                      |                                     |               |
|   After 1 additional host failure                                                           | limit1hf                            | Normal        |
|   Current cluster situation                                                                 | limit0hf                            | Normal        |
|   Host component limit                                                                      | nodecomponentlimit                  | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Network                                                                                     |                                     |               |
|   Active multicast connectivity check                                                       | multicastdeepdive                   | Normal        |
|   All hosts have a vSAN vmknic configured                                                   | vsanvmknic                          | Normal        |
|   All hosts have matching multicast settings                                                | multicastsettings                   | Normal        |
|   All hosts have matching subnets                                                           | matchingsubnet                      | Normal        |
|   Hosts disconnected from VC                                                                | hostdisconnected                    | Normal        |
|   Hosts with connectivity issues                                                            | hostconnectivity                    | Normal        |
|   Multicast assessment based on other checks                                                | multicastsuspected                  | Normal        |
|   Network latency check                                                                     | hostlatencycheck                    | Normal        |
|   vMotion: Basic (unicast) connectivity check                                               | vmotionpingsmall                    | Normal        |
|   vMotion: MTU check (ping with large packet size)                                          | vmotionpinglarge                    | Normal        |
|   vSAN cluster partition                                                                    | clusterpartition                    | Normal        |
|   vSAN: Basic (unicast) connectivity check                                                  | smallping                           | Normal        |
|   vSAN: MTU check (ping with large packet size)                                             | largeping                           | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Performance service                                                                         |                                     |               |
|   All hosts contributing stats                                                              | hostsmissing                        | Normal        |
|   Performance data collection                                                               | collection                          | Normal        |
|   Performance service status                                                                | perfsvcstatus                       | Normal        |
|   Stats DB object                                                                           | statsdb                             | Normal        |
|   Stats DB object conflicts                                                                 | renameddirs                         | Normal        |
|   Stats master election                                                                     | masterexist                         | Normal        |
|   Verbose mode                                                                              | verbosemode                         | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Physical disk                                                                               |                                     |               |
|   Component limit health                                                                    | physdiskcomplimithealth             | Normal        |
|   Component metadata health                                                                 | componentmetadata                   | Normal        |
|   Congestion                                                                                | physdiskcongestion                  | Normal        |
|   Disk capacity                                                                             | physdiskcapacity                    | Normal        |
|   Memory pools (heaps)                                                                      | lsomheap                            | Normal        |
|   Memory pools (slabs)                                                                      | lsomslab                            | Normal        |
|   Operation health                                                                          | physdiskoverall                     | Normal        |
|   Physical disk health retrieval issues                                                     | physdiskhostissues                  | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| Stretched cluster                                                                           |                                     |               |
|   Invalid preferred fault domain on witness host                                            | witnesspreferredfaultdomaininvalid  | Normal        |
|   Invalid unicast agent                                                                     | hostwithinvalidunicastagent         | Normal        |
|   No disk claimed on witness host                                                           | witnesswithnodiskmapping            | Normal        |
|   Preferred fault domain unset                                                              | witnesspreferredfaultdomainnotexist | Normal        |
|   Site latency health                                                                       | siteconnectivity                    | Normal        |
|   Unexpected number of fault domains                                                        | clusterwithouttwodatafaultdomains   | Normal        |
|   Unicast agent configuration inconsistent                                                  | clusterwithmultipleunicastagents    | Normal        |
|   Unicast agent not configured                                                              | hostunicastagentunset               | Normal        |
|   Unsupported host version                                                                  | hostwithnostretchedclustersupport   | Normal        |
|   Witness host fault domain misconfigured                                                   | witnessfaultdomaininvalid           | Normal        |
|   Witness host not found                                                                    | clusterwithoutonewitnesshost        | Normal        |
|   Witness host within vCenter cluster                                                       | witnessinsidevccluster              | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| vSAN Build Recommendation                                                                   |                                     |               |
|   vSAN Build Recommendation Engine Health                                                   | vumconfig                           | Normal        |
|   vSAN build recommendation                                                                 | vumrecommendation                   | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
| vSAN iSCSI target service                                                                   |                                     |               |
|   Home object                                                                               | iscsihomeobjectstatustest           | Normal        |
|   Network configuration                                                                     | iscsiservicenetworktest             | Normal        |
|   Service runtime status                                                                    | iscsiservicerunningtest             | Normal        |
+---------------------------------------------------------------------------------------------+-------------------------------------+---------------+
~~~

The one we want to disable is `controlleronhcl`. We can then use the `vsan.health.silent_health_check_configure` command with the cluster `/localhost/Contoso/computers/Corp` and the switch `--add-checks 'controlleronhcl'`:

~~~ bash
> vsan.health.silent_health_check_configure /localhost/Contoso/computers/Corp --add-checks 'controlleronhcl'
Successfully add check "SCSI controller is VMware certified" to silent health check list for Corp
~~~

If we then run a Health check we can see that the HCL check is skipped:

~~~ bash
> vsan.health.health_summary /localhost/Contoso/computers/Corp
Overall health: green (OK)
+-----------------------------------------+---------+
| Health check                            | Result  |
+-----------------------------------------+---------+
|   SCSI controller is VMware certified   | skipped |
+-----------------------------------------+---------+
~~~

You can see the overall health is now green, and in the GUI:

![vSAN Health Skipped]({{ site.url }}/assets/images/vSAN-Health-Skipped.png)

## Wrap Up

Now that check is skipped the warning is gone from my Cluster and my OCD is happy.

The RVC seems to be a useful tool for checking and manipulating vSphere and vSAN so I am going to spend some more time investigating what can be done with it.

I hope this post helps others that run a nested vSAN lab.