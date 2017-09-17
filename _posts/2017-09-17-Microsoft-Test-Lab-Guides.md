---
layout: single
title: "Microsoft Test Lab Guides"
date: 2017-09-17
category: [Microsoft]
excerpt: "A gem of a resource from Microsoft to create various test labs"
---
From reading the lots of blogs, websites, podcasts, social media, etc. I don't really hear much love for the [Microsoft Test Lab Guides](https://social.technet.microsoft.com/wiki/contents/articles/1262.test-lab-guides.aspx) (TLG). I personally think it's a great resource for learning various Microsoft technologies. I use the Base Configuration to create a stable Active Directory domain which I use in my vSphere nested lab and a copy for testing AD changes before I perform them at work in production.

These are a series of instructions for building a Base Configuration consisting of a Domain Controller, Application Server and Client in a Corporate subnet and an Edge and Internet IIS server in an Internet subnet.

![Base Configuration]({{ site.url }}/assets/images/5315.BaseConfig_NewIcons.png)

The Base Configuration comes in three flavours:

* Base Configuration for Windows Server 2012 R2 and Windows 8.1
* Base Configuration for Windows Server 2012 and Windows 8 Jump
* Base Configuration for Windows Server 2008 R2 and Windows 7

Unfortunately there is no Windows Server 2016 and Windows 10 base configuration yet.

The guide details all the steps to setup the VM's in both detailed manual steps and handily at the end of each section the PowerShell commands to complete the steps. By cutting and pasting the commands into the lab VM's you can easily get the whole lab setup in less than an hour (assuming you have pre-built VM templates).

There are Public Cloud Configurations which connect the Corporate subnet to Azure and Office 365, and also pure Azure configurations.

After the Base Configuration is built there are Modular TLGs that help you setup the lab to demonstrate and play with a particular Microsoft product or technology. Some examples are:

* SQL Server 2012 Enterprise
* IPv6
* Remote Desktop Services
* Hyper-V
* DirectAccess
* System Center
* SharePoint Server

The list is pretty extensive. Some of the guides are community authored such as the Modular TLG "[Contoso branch office across a simulated private WAN link](https://social.technet.microsoft.com/wiki/contents/articles/16021.test-lab-guide-configure-the-contoso-branch-office.aspx)".

One point to note there don't seem to have been many updates to the TLGs in a while. As noted above no 2016 TLGs have been created. I am not sure why but I see this as an opportunity for someone to create TLGs in the community and contribute back. If I had the time I certainly would as I have gained a lot from them.

Also note they are a *guide*. I have modified the Base Configuration to suit my own needs such as IP scheme, server names, etc. I find it useful to create a stable Microsoft environment for a variety of uses.

Please go ahead and check them out.