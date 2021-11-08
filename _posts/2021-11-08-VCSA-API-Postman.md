---
layout: single
classes: wide
title: "Using Postman with the vCenter API"
date: 2021-11-08
category: [VMware]
excerpt: "How to authenticate and then pull a list of VMs from the vCenter API using Postman"
---
## Introduction

As an Operations person learning skills such as APIs will be essential in the future, if not already. I decided on an afternoon off work to spend time trying to use the vCenter API. To make my life easier I decided to use [Postman](https://www.postman.com/) as it would help 'guide' me through accessing the API. This was my first time using an API and I hit an initial hurdle - authentication.

The [VMware Developer Documentation](https://developer.vmware.com/apis) is very comprehensive but it assumes you know enough to understand basic API concepts which I didn't. Example code was using CURL and PowerShell to access the API but I was using Postman. In the post I will document how to authenticate to vCenter, perform a simple request (get a list of VMs), and finally close the session.

This was performed using vCenter Server 7.0 Update 3a/Build 18778458, and Postman v9.1.3 running on Windows 10.

> Disclaimer: In no way am I using Postman properly to do this. For example I am hard coding my vCenter name, not using variables, etc. This post gives you enough to call your first API.

### First Attempt to get List of VMs

In vCenter is there is a great API Explorer available on `https://<VCSA-FQDN>/ui/app/devcenter/api-explorer` or in the GUI navigate to here:

![API Explorer]({{ site.url }}/assets/images/vcsa-api-explorer-01.png)

I changed the `Select API` drop down to vcenter and the first option there was VM. Expanding this then the `GET` section shows this is what we want - a list of the VMs on the vCenter. You can then keep scrolling down to the `Try it out` section and hit `Execute`:

![API Explorer Try it out]({{ site.url }}/assets/images/vcsa-api-explorer-02.png)

This shows the CURL request you can use and the Response which was the three VMs I had running on this clean install.

This is great but I wanted to do it myself using Postman.

### First Attempt to List VMs

Looking at the CURL API Request from the API Explorer it showed it was a `GET` to the URL `https://vcsa01.ad.vgemba.com/api/vcenter/vm`. I opened Postman and created a new request. To do this in Postman click on Collections then click the `+` icon in the Scratch Pad:

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-01.png)

This opens an new request where we can enter the type of request, the URL and then Send it:

1. Change the method to GET
2. Enter the URL `https://vcsa01.ad.vgemba.com/vcenter/vm`
3. Click Send

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-02.png)

It seems it was sent ok but it has returned a message saying we were `UNAUTHENTICATED`. This makes sense. You always need to authenticate when using vCenter.

We can see an Auth tab in the request, so how about putting in a username and password that can log into vCenter? Changing the Type to Basic and entering the username and password results in:

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-03.png)

Still no authentication.

### API Authentication

I went back to the [VMware Developer Documentation](https://developer.vmware.com/apis) and eventually found the section on Authentication for the vCenter API - [CIS Session APIs](https://developer.vmware.com/apis/vsphere-automation/latest/cis/cis/session/). This section on the [POST Method](https://developer.vmware.com/apis/vsphere-automation/latest/cis/api/session/post/) seems to be what we want:

> Creates a session with the API. This is the equivalent of login. This operation exchanges user credentials supplied in the security context for a session token that is to be used for authenticating subsequent calls. To authenticate subsequent calls clients are expected to include the session token. For REST API calls the HTTP vmware-api-session-id header field should be used for this.

It helpfully gives an example using PowerShell but how do we do this in Postman? Again we create a new request and this time we do the following:

1. Change the method to POST
2. Enter the URL `https://vcsa01.ad.vgemba.com/api/session`
3. Click the Auth tab
4. Change the Auth Type to `Basic Auth`
5. Enter the `Username` and `Password`
6. Click Send

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-04.png)

You can see we get a text string back. According to the documentation the response is:

>201 Created returns string formatted as password of type application/json. Newly created session token to be used for authenticating further requests

Looks like we have a token we can use to authenticate. Take a note of this string.

### Second Attempt to List VMs

Looking at the documentation for [GET VMs](https://developer.vmware.com/apis/vsphere-automation/latest/vcenter/api/vcenter/vm/vm/get/) under the Header Parameters section it says you need to need a key string called `vmware-api-session-id` with what looks like the string we got back when we authenticated in the previous section.

Now we can go back to the Auth tab in our request to GET a list of VM's:

1. Change the Auth Type to `API Key`
2. For Key enter `vmware-api-session-id`
3. For Value enter the string returned earlier
4. The documentation says this is a Header parameter, so in the Add to drop down ensure it is `Header`

Now clicking Send gives us:

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-05.png)

Finally a success! We have a list of the VMs that matches what we saw through the API Explorer. Now we know how to authenticate we can use the same method to run any API request against the vCenter.

### Clean up - Ending the Session

We always log out of vCenter properly, so using the API should be no different. We should clean up after ourselves and end the session. Looking at the documentation again we can see there is a [Delete Session](https://developer.vmware.com/apis/vsphere-automation/latest/cis/api/session/delete/) API.

1. Change the Method to `DELETE`
2. Under Auth make sure the Auth Type is `API Key`
3. Use the same key as before `vmware-api-session-id`
4. For Value enter the string we have been using
5. Add to drop down ensure is `Header`

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-06.png)

Sending the request returns nothing, so the session token is gone. We can test this by trying to use it again to list the VMs:

![VCSA Postman]({{ site.url }}/assets/images/vcsa-postman-07.png)

Again we see the `UNAUTHENTICATED` message in the Body.

### Wrap Up

This blog post this may seem incredibly easy (or dumb!) to some people, but I spent a couple of hours figuring this out as Google wasn't much help for me. As I said before this is no way the best and proper way to use Postman and some might even say why use Postman. I personally find using a graphical interface when learning something new is easier for me before I move to the console.

Hopefully I can help someone else save the time that wants to investigate the vCenter REST API using Postman.
