---
layout: single
title: "vCenter 6.7U1, NSX Manager, Custom Certificates and Lookup Service Error"
date: 2019-03-03
category: [VMware]
excerpt: "A 'fix' for an issue adding NSX Manager to the Lookup Service when Custom Certificates are deployed"
---
# Introduction

One of my colleagues at work is setting up a greenfield vCenter deployment with the addition of NSX v6.4.3. He hit an interesting problem recently that involved custom certificates and the Lookup Service.

In the end it required a call to GSS to resolve but I thought I would document the issue in case someone else hit it.

## Background

There were two vCenter Appliances with embedded PSC's running in Enhanced Linked Mode both v6.7 Update 1. Custom certificates from an internal PKI had been applied to the vCenters in [Hybrid Mode](https://blogs.vmware.com/vsphere/2017/01/walkthrough-hybrid-ssl-certificate-replacement.html)

This was working well and no reported issues. Next up was to integrate NSX v6.4.3 into the environment.

## The Issue

When logged into NSX Manager the issue occurred when trying to add it to the VCSA Lookup Service. The following error was shown:

![NSX Manager Error]({{ site.url }}/assets/images/NSX_Manager_Error.png)

`NSX Management Service operation failed.( Initialization of Admin Registration Service Provider failed. Root Cause: The SSL certificate of STS service cannot be verified )`

That was interesting to note was the Thumbprint presented did match the custom certificate deployed in vCenter.

NSX Manager was joined to vCenter successfully. No matter what was tried we could not get NSX Manager to connect to the lookup service.

## Troubleshooting

I posted the issue we were facing in the vExpert Slack NSX channel and the awesome [Laurens van Duijn](https://twitter.com/LaurensvanDuijn) happened to be experiencing the same issue. Two vCenter 6.7 U1, linked mode, and NSX 6.4.4. He did a lot of troubleshooting on this. He said he had done the same process lots of times but the difference in this case was this was the first time on 6.7U1 and an embedded PSC.

[Wesley Geelhoed](https://twitter.com/wessieloerus) also did some testing and deployed the same setup from scratch, but without deploying custom certificates. NSX Manager could connect to the Lookup Service successfully. The Custom Certificates were the smoking gun.

Finally [Iain Worsfold](https://vexpert.vmware.com/directory/967) chimed in on the conversation. He had experienced a similar issue and had to engage support to resolve the issue.

## The 'Fix'

The preventative solution is to join NSX Manager to vCenter and the Lookup Service **before** deploying any custom certificates. In our case we didn't want to blow away all the work completed already so we opened a case with VMware Global Support Services.

The cause of the issue is the certificate tool in vCenter/PSC did not replace some of the many certificates properly. GSS ran a tool called `ls_ssltrust_fixer.py` that scanned for the suspect certificates and replaced them correctly. After that NSX Manager could connect to the Lookup Service.

## Wrap Up

We got the issue resolved but having to engage Support is not ideal. There must be an issue with the Certificate tool in vCenter that needs to be resolved. I am sure we will not the only ones that will have this issue. To be honest I'm not the only one that thinks Certificates in vCenter have always been a nightmare to deal with. Something to work on VMware!

I want to say thanks to Laurens, Wesley and Iain for the assistance. It's another example of the amazing vExpert Community helping out. Having access to the community via Slack is one of the most valuable benefits of being a vExpert.