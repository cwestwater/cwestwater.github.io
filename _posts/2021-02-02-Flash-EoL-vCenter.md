---
layout: single
classes: wide
title: "Flash EoL and vCenter"
date: 2021-02-02
category: [VMware]
excerpt: "As of January 2021 Flash is EoL. How can you still use it for vCenter?"
---
## Introduction

As of January 2021 Adobe Flash is now End of Life.  This is great news as Flash is a bad product that was inefficient, insecure and legacy. However if you are using vCenter 6.7 Update 1 or earlier not all features in vCenter have made it to the HTML5 client. [KB2150338](https://kb.vmware.com/s/article/2150338) for reference.

If you are running for example vCenter 6.5 how do you access the legacy Flash client? Let's look at what you can do.

## VMware Guidance

VMware have published [KB78589 - VMware Flash End of Life and Supportability](https://kb.vmware.com/s/article/78589) that details what you can do.

The real fix to this problem is to upgrade your vSphere environment to at least 6.7 Update 1 as I keep seeing people berate people on Twitter, Reddit, etc. However a lot of people live in the real world where they just haven't planned, ran out of time, or can't upgrade to a version that resolves this issue.

The workaround I'm going to look at today is to create a custom mms.cfg file. My lab is already at 6.7 Update 3k but the Flash client is still there and accessible (you will get a depreciated notification in a banner). Chrome is v87.0.4280.141 installed under the user context.

## What Happens without the Workaround

So what happens when you try to access the Flash client after Flash is depreciated?

![Flash Error]({{ site.url }}/assets/images/vCenter-Flash-01.png)

The big flash logo is clickable and leads you to [Adobe Flash Player EOL General Information Page](https://www.adobe.com/products/flashplayer/end-of-life.html).

## Create an mms.cfg File

As per the KB documentation and our setup, we need to create the file in the folder `%localappdata%\Google\Chrome\User Data\Default\Pepper Data\Sockwave Flash\System`. In my case the System folder did not exist so I had to create it.

Next is to create a file called `mms.cfg` using a text editor such as Notepad. The file needs to be created with these lines:

```shell
EOLUninstallDisable=1
EnableAllowList=1
AllowListPreview=1
AllowListUrlPattern=https://vcsa1.corp.contoso.com/
```
Lets go through the lines:

```shell
EOLUninstallDisable=1
```

Disable prompts to uninstall Flash Player now it is end of life.

```shell
EnableAllowList=1
```
Set Flash to only allow content from a set of allowed URLs.

```shell
AllowListPreview=1
```

This allows requests matching a patterns are allowed.

```shell
AllowListUrlPattern=https://vcsa1.corp.contoso.com/
```

FInally this specifies the top level URL for the vCenter. Just replace the URL with your vCenter address.

Save the file. Now we can try to login to the Flash client to check it loads:

![Flash Error]({{ site.url }}/assets/images/vCenter-Flash-02.png)

It's working and you can see the Flash interface.

What happens if you have multiple vCenters? Just add another `AllowListUrlPattern` line to the `mms.cfg` file.

## Wrap Up

This should be treated as a temporary workaround. The best fix is to upgrade your systems so they don't use Flash.  Good luck.