---
layout: single
title: "DellEMC Isilon Simulator - Part 2"
date: 2019-04-30
category: [VMware]
excerpt: "How to setup a Dell EMC Isilon four node cluster on VMware Workstation, Part 2"
---
# Introduction

In [Part 1]({{ site.baseurl }}{% post_url 2019-04-23-DellEMC-Isilon-Simulator-Part1 %}) I ran through setting up the first node of four in the Isilon Cluster on VMware Workstation.

Now I will run through adding nodes two to four and some basic setup of the cluster.

## Adding Additional Nodes

Like the previous post I simply open the OVA in Workstation and name it. Next I change al the NICs to the LAN segment:

![Isilon Node 2]({{ site.url }}/assets/images/Isilon-Node2-01.png)

Once the VM is powered on it will ask you if you want to format the ifs partition on all drives. As before type `yes`:

![Isilon Node 1 Setup]({{ site.url }}/assets/images/Isilon-Node1-01.png)

Once again the same initial wizard appears. This time I want to press `2` to `Join and existing cluster`:

![Isilon Node 2]({{ site.url }}/assets/images/Isilon-Node2-02.png)

The Node will find the cluster setup on the LAN. Type 1 to join:

![Isilon Node 2]({{ site.url }}/assets/images/Isilon-Node2-03.png)

The Node runs some scripts and joins the cluster:

![Isilon Node 2]({{ site.url }}/assets/images/Isilon-Node2-04.png)

You can confirm the Node has joined the cluster by going into the GUI:

![Isilon Node 2]({{ site.url }}/assets/images/Isilon-Node2-05.png)

You can see that Node 2 has just taken the next available IP address in the pool we defined when setting up Node 1.

Do add Nodes 3 and 4 simply repeat the steps above. Once all are added to the cluster it will look as so:

![Isilon Cluster]({{ site.url }}/assets/images/Isilon-Cluster-01.png)

It all looks good!

## Licensing

Now the cluster is up I need to license it. SSH into the cluster, authenticate and run the command `isi license list`

~~~ bash
ISILON-1# isi license list
OneFS License ID: -
 Valid Signature: No

Module                Licensed node count  Actual node count  Status     Expiration date
-----------------------------------------------------------------------------------------
SMARTQUOTAS           0 Nodes              0 Nodes            Unlicensed -
SNAPSHOTIQ            0 Nodes              0 Nodes            Unlicensed -
SMARTCONNECT_ADVANCED 0 Nodes              0 Nodes            Unlicensed -
SYNCIQ                0 Nodes              0 Nodes            Unlicensed -
SMARTPOOLS            0 Nodes              0 Nodes            Unlicensed -
SMARTLOCK             0 Nodes              0 Nodes            Unlicensed -
HDFS                  0 Nodes              0 Nodes            Unlicensed -
SMARTDEDUPE           0 Nodes              0 Nodes            Unlicensed -
CLOUDPOOLS            0 Nodes              0 Nodes            Unlicensed -
SWIFT                 0 Nodes              0 Nodes            Unlicensed -
HARDENING             0 Nodes              0 Nodes            Unlicensed -
ONEFS                 0 Nodes              4 Nodes            Unlicensed -
-----------------------------------------------------------------------------------------
Total: 12
~~~

To generate evaluation licenses and apply them run the command `isi license add --evaluation ONEFS,SMARTQUOTAS,SNAPSHOTIQ,SMARTCONNECT_ADVANCED,SYNCIQ,SMARTPOOLS,SMARTLOCK,HDFS,SMARTDEDUPE,CLOUDPOOLS,SWIFT,HARDENING`. The licenses are evaluation, but do not expire:

~~~ bash
ISILON-1# isi license add --evaluation ONEFS,SMARTQUOTAS,SNAPSHOTIQ,SMARTCONNECT_ADVANCED,SYNCIQ,SMARTPOOLS,SMARTLOCK,HDFS,SMARTDEDUPE,CLOUDPOOLS,SWIFT,HARDENING
~~~

The license summary is shown. It took me a while to figure out how to accept it. It looks like it opens in vi, so type `:q` to get the acceptance prompt:

~~~ bash
Software, and are waiving any rights, to the maximum extent permitted by
applicable law, to any claim anywhere in the world concerning the
enforceability or validity of such written signed agreement.
/LICENSE.txt :q

I have read and agree to be bound by the terms and conditions of the EULA. I
represent that I am authorized to legally bind the entity licensing this
software. ? (yes/[no]): yes
~~~

Then to confirm:

~~~ bash
ISILON-1# isi license list
OneFS License ID: -
 Valid Signature: No

Module                Licensed node count  Actual node count  Status     Expiration date
-----------------------------------------------------------------------------------------
SMARTQUOTAS           4 Nodes              4 Nodes            Evaluation -
SNAPSHOTIQ            4 Nodes              4 Nodes            Evaluation -
SMARTCONNECT_ADVANCED 4 Nodes              4 Nodes            Evaluation -
SYNCIQ                4 Nodes              4 Nodes            Evaluation -
SMARTPOOLS            4 Nodes              4 Nodes            Evaluation -
SMARTLOCK             4 Nodes              4 Nodes            Evaluation -
HDFS                  4 Nodes              4 Nodes            Evaluation -
SMARTDEDUPE           4 Nodes              4 Nodes            Evaluation -
CLOUDPOOLS            4 Nodes              4 Nodes            Evaluation -
SWIFT                 4 Nodes              4 Nodes            Evaluation -
HARDENING             4 Nodes              4 Nodes            Evaluation -
ONEFS                 4 Nodes              4 Nodes            Evaluation -
-----------------------------------------------------------------------------------------
Total: 12
~~~

You can see we are licensed for all options, using evaluation licenses, with no expiration.

## NTP Setup

Next up is NTP setup. I first check NTP setup, then point towards my domain controller in the lab using command `isi_ntp_config add server <hostname>` and then check with `isi_ntp_config list`:

~~~ bash
ISILON-1# isi_ntp_config add server dc1.corp.contoso.com
dc1.corp.contoso.com is added.
NTP Server Setting:
dc1.corp.contoso.com

ISILON-1# isi_ntp_config list
NTP Server Setting:
dc1.corp.contoso.com

Nodes excluded from contacting external NTP servers:
No nodes are excluded from contacting external NTP servers.
Chimers has not been modified from the default (3)

NTP Auth Keyfile:
No Keyfile is configured.
~~~

## Active Directory Authentication

Last up is AD authenitcation. You need to add the domain using the CLI command `isi auth ads create` and defining the domain and a suitable username. You will be prompted for the account password. Finally I check the domain has been added ok with `isi auth ads list`:

~~~ bash
ISILON-1% isi auth ads create corp.contoso.com a-cwestwater@corp.contoso.com
password:

ISILON-1% isi auth ads list
Name             Authentication  Status  Site
----------------------------------------------------------------
CORP.CONTOSO.COM Yes             online  Default-First-Site-Name
----------------------------------------------------------------
Total: 1
ISILON-2%
~~~

## Wrap Up

Now I have a four node Isilon cluster setup in Workstation that is ready for me to learn the system. I look forward to learning more about Isilon!