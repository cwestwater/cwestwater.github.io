---
layout: single
title: "Scottish VMUG - 26th April 2018"
date: 2018-04-29
category: [VMUG]
excerpt: "Recap of the April 2018 Scottish VMUG"
---
# Introduction

On Thursday 26th April 2018 the second Scotland VMUG of the year happened. It was a great day and evening of technical content, community sessions and chat.

## Technical/Sponsor sessions

On the line up were two very big names. First up for the day was [Cormac Hogan](https://twitter.com/CormacJHogan) from VMware talking about 'What's happening in the world of VMware Storage'. He started off with the point that data is always growing and how do you keep it safe, available, confidential and ensure integrity? VMware are saying the foundation to this is [Storage Policy Based Management](https://blogs.vmware.com/virtualblocks/2017/01/16/understanding-storage-policy-based-management/). In the presentation Cormac covered:

* vSAN
* VVOLS
* Core storage enhancements in vSphere 6.5
* [Project Hatchway](https://blogs.vmware.com/cloudnative/2017/09/06/project-hatchway-persistent-storage-cloud-native-applications/) which is persistant storage for containers

Next up was [Cody Hosterman](https://twitter.com/codyhosterman) from [Pure Storage](https://www.purestorage.com/) talking about VVols. I had only looked as VVols as part of my VCP study so didn't really 'get it'. However after Cody's presentation the penny dropped.

VVols are a way of simplifying the setup of storage while providing value added capabilities to individual VM's such as replication, encryption, compliance and SLA's. Of course all this is controlled by Storage Policy Based Management. I think we all came away with a much clearer understanding of VVols. I personally don't think VMware are doing a good job of promoting the advantages of VVols.

Next up was Andrew Hughes from HPE telling us about [HPE Infosight](https://www.hpe.com/uk/en/product-catalog/storage/storage-software/pip.hpe-infosight.1010331235.html). Infosight provides answers to problems with your system. It collects data from the storage (with plans for expansion to the full stack) and provides pro-active support, analytics from VMware, planning recommendations, and can even save you from yourself when applying updates that could hose your system. It's available for Nimble storage, but I was please to hear it's coming to Simplivity soon.

## Community Sessions

After lunch was the start of the community sessions. There were three different presentations which I attended.

First up was Martin Campbell and Neil Cooper from the University of Edinburgh talking about [VMware Integrated OpenStack](https://www.vmware.com/products/openstack.html) in the Real World. Even though I will never use OpenStack at work it was interesting to hear the journey Martin and Neil went through to stand it up and make it available to the developers. It seems like they are very happy with the solution they have stood up and the users have bought into it.

Second up was [Brian Gerrard](https://twitter.com/vBeeGee) and his colleague, double VCDX, [Konrad Clapa](https://twitter.com/clapa_konrad). They were talking about vRealize Operations and Automation at Atos. It was a session packed with tips, hard won experiences and DevOps practices. One thing I picked up was they developed their own best practices to fit them. All too often I see people chasing 'best practices' and not stopping to think if they are actually relevant to them.

Last on the bill was [Craig Dalrymple](https://twitter.com/cragdoo) on Making your first RESTful API call to VMware. I was intersted in this as I know it should be on my radar as something I need to look at, but the furthest I have got is downloading Postman. It was great (and very brave of Craig) to do some live demos to show us the practical application of API calls to vCenter. It certainly got me interested in starting to look into this.

All available slide decks should be available on the [Scottish VMUG](http://scottishvmug.com/) blog site soon.

## The Community and VMUG

As usual the best part of the day for me was interacting with the Community. [Chris Storrie](https://twitter.com/chrisstorrie), one of the VMUG Leaders launched a new initiative called the Scottish VMUG Coommunity. Out of the blue he made myself, Craig Dalrymple and Martin Campbell stand up and asked everyone that was new to the VMUG track us down and speak to us. Sounds simple right? I think it's a great idea. For a long time I came to the VMUG, didn't speak to anyone and left right away. I really started getting value of it it once I spoke to people. He's asked people that contribute to the VMUG contribute again by being someone anyone should feel comfortable going and speaking to. I am really excited about this initiative and I kicked off by speaking to three new people over the day and evening.

Over the course of the event I had many conversations about a range of topics to a great bunch of people. Chris introduced me to Cody and initially I was a bit nervous but I soon realised he is like all of us there - a passionate technologist. Don't be nervous or reluctant to speak to the big names in the community!

I spoke to people about gin, the many choices of certifications, careers, Microsoft Azure, the success of the VMUG (bigger than most), drones, backups, and lots of other things. Great conversations with great people.

If you want to get involved in the community there are a couple of easy ways to do it apart from attending the VMUGs. The first is the very active Scottish VMUG slack channel. Email <scotland@vmug.com> for access. The second is to post on the Scottish VMUG [Community Blog]({{ site.baseurl }}{% post_url 2017-10-31-Scottish-VMUG-Community-Blog %}). You can post once, or many times. It's simply a great way of contributing back to the community by writing about something others will find interesting.

Thanks to the sponsors:

![Pure Storage]({{ site.url }}/assets/images/PureStorage.jpg)
![Softcat]({{ site.url }}/assets/images/Softcat.jpg)
![Nakivo]({{ site.url }}/assets/images/Nakivo.png)
![HPE]({{ site.url }}/assets/images/HPE.jpg)
![IGEL]({{ site.url }}/assets/images/IGEL.jpg)
![SITS Group]({{ site.url }}/assets/images/SITS-Group.jpg)

Especially [SITS Group](https://www.sitsgroup.com/) for the vBeers sponsorship!

As usual I need to thank the VMUG Leaders [Sandy Bryce](https://twitter.com/sandybryce), [Chris Storrie](https://twitter.com/chrisstorrie), [James Cruickshank](https://twitter.com/vCrooky) and [Iain Balmer](https://twitter.com/Balmeri) for organising the day. A grand job as always!