---
layout: single
title: "The Importance of VCSA Services"
date: 2019-08-11
category: [VMware]
excerpt: "How one VCSA service that was not running manifested in multiple issues in vCenter. It also taught me a valuable lesson."
---
# Introduction

This week I was trying to deploy an OVA on a vCenter and it was throwing strange error messages. I chalked it down to the Flash client being dumb, but then I could not add a new Role for some permissions.

Once I started I discovered the issue it reminded me the importance of checking the basics.

This was on a VCSA 6.5 Update 1 with an external PSC.

## Symptoms

As I said above the first sign of an issue was on the deployment of an OVA. In the Flash client I kept getting this error when selecting Deploy OVF Template:

> This version of vCenter Server does not support Deploy OVF Template using this version of the vSphere Web Client. To Deploy OVF Template, login with version 6.5.0.0 of vSphere Web Client.

![OVA Deployment Error]({{ site.url }}/assets/images/vcsa-service-error-01.png)

I tried then using the HTML5 client and it would just sit at Validating at step three in the wizard:

![OVA Deployment Error]({{ site.url }}/assets/images/vcsa-service-error-02.png)

I had a look at the VMware Knowledgebase and found [KB2151085](https://kb.vmware.com/s/article/2151085) but this was originally a new VCSA and had not been upgraded. I ran out of time to investigate further so ended up using PowerCLI to deploy the OVA I needed.

The next day I needed to create a new Role in vCenter and encountered further issues. The first was when I went to the Roles screen in the Flash client. It said I did not have permissions. I then tried using the SSO admin and had the same:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-03.png)

Again I checked the HTML5 client and found an empty screen:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-04.png)

As a final check I went to an existing folder and tried to add an existing permission to the folder. Going to the Add Permission wizard in the Flash client I got the error:

> An internal error has occurred - Error #1034

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-05.png)

and the error stack showed:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-06.png)

Googling `Error #1034` showed a couple of entries and they fixed it by rebooting the appliance.

## The Fix

At this point I knew I had a serious problem. My first thought would be to reboot the appliance but this would have involved a Change ticket with approvals and an out of hours reboot which I wanted to avoid.

I spent time wondering if there was a permissions error but I was trying the SSO local administrator account which has access to everything. I spent some time looking at the SSO configuration and the Groups but could not find the issue. I could rule out permissions as the cause.

Next I spent time looking at KB2151085 from above. I performed the steps listed even though I knew is was a bigger issue but wanted to rule it out. Of course that didn't help.

At this point it was lunch time. As many of you know there is nothing better to help you figure out a problem than walking away from it for a while. So I went for a walk! When I got back I decided to get back to basics and check on the health of the appliance.

I checked the following in the VAMI:

1. Health Status (all green)
2. NTP status
3. DNS setup
4. Database utilization (had issues when VCSA disk partitions fill up)

That all looked good.

Then I moved to the vCenter UI. I had a look at the System Configuration under Administration and noticed in the Services Health a Warning:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-07.png)

Clicking on the Objects tab I could see the `VMware vCenter Server` service was in Warning. Looking further down the list I noticed something. The service `vAPI Endpoint` was stopped:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-08.png)

I knew the `vAPI Endpoint` is a critical vCenter service. Checking the properties of the the service showed it should have been running as the Startup Type was Automatic:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-09.png)

I started up the service and went back to the Roles screen using my domain account and could see Roles now:

![Roles Error]({{ site.url }}/assets/images/vcsa-service-error-10.png)

I then check a simple OVA deployment and it worked.

## Wrap Up

This taught me a lesson. I spent too long on Google and searching for a complex error and fix. I need to start with the basics first.

I also took note in our wiki of the services that should be running on the vCenter and PSC so that others not as familiar will know what should and should not be running.

I also wanted to blog this as the specific error codes matched other issues and KB articles that were not relevant. If anyone else hits similar problems I hope they land on this post and it helps them.
