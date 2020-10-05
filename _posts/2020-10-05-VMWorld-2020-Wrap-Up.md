---
layout: single
title: "VMworld 2020 Recap"
date: 2020-10-05
category: [VMware]
excerpt: "Annoucements, my opinion and recommended sessions from VMworld 2020"
---

## Introduction

I recently published [this post]({{ site.baseurl }}{% post_url 2020-07-27-VMworld-2020 %}) about VMworld 2020. This event just ended so I thought I would share some of the major announcements, some of the sessions I attended and my opinions of the event.

### Announcements

There was a firehose of announcements during the days leading up to and during VMworld. The ones that caught my eye were:

1. [Acquisition of SaltStack](https://blogs.vmware.com/management/2020/09/saltandvra.html). If I'm not mistaken one of the last independent configuration management/automation tools. This will obviously be tightly integrated with vRealize to extend the capabilities of that product. This will be interesting to watch how the products merge together.
2. [Project Monterey](https://blogs.vmware.com/vsphere/2020/09/announcing-project-monterey-redefining-hybrid-cloud-architecture.html). From reading all the marketing fluff I think this boils down to running ESXi on 'SmartNICs' which I assume will be small ARM based CPU's. This should allow you to offload things like firewall applications to the NICs instead of running on core vSphere.
3. [VMware Cloud Disaster Recovery](https://blogs.vmware.com/virtualblocks/2020/09/29/announcing-vmware-cloud-disaster-recovery/). After VMware purchased Datrium this is a VMware branded version of Cloudshift. This looks interesting for Cloud based DR instead of lots of expensive hardware just sitting waiting for an event. The CFOs will love this option!
4. [Carbon Black Cloud Workload](https://www.carbonblack.com/press-releases/vmware-delivers-intrinsic-security-to-the-worlds-digital-infrastructure/). Another acquisition Carbon Black this will extend the capabilities of the product across workloads both in vCenter and across clouds. This is trying to be **the** security product across the various attack vectors. What is cool everyone with licensed vSphere 6.5 will get a [free six month trial](https://www.carbonblack.com/workload-free-trial/).

There was a lot of announcements regarding AI/ML, 5G, Tanzu, etc. but these are way outside of my responsibilities and to be honest my interests, so I didn't actively investigate these. However if you want to know more, the [VMworld 2020 Media Kit](https://www.vmware.com/company/news/media-resources/vmworld-2020-media-kit.html) is a great place to start.

### Sessions

As ever, the range and quality of the sessions was outstanding. The sessions were filterable as so:

![VMworld 2020 Sessions]({{ site.url }}/assets/images/VMworld-2020-Sessions.png)

You can see the range of topics, audiences, techincal levels, industries, job roles, that are covered across the sessions. There is something for everyone there.

During VMworld there were two types of sessions I participated in:

- 'Live' Sessions which ran three times that were suitable for US/EMEA/APAC timezones
- Breakout Sessions that were available on demand

Now that the event is over, over 930 sessions are now available on demand (from both types of session) on the [VMworld website](https://www.vmworld.com/en/index.html) - registration required.

From the sessions I watched during VMworld I particularity liked these ones:

- ETPD2087 - Achieving Happiness: Building Your Brand and Your Career. [Amanda Blevins](https://twitter.com/AmandaBlev) hosted probably my favourite session. It covers how to be happy in your career (note career not job) and build your professional brand. Please watch this session
- ETPD1986 - Finding My Way - Who I am, and What I Can Offer. [Katherine Skilling](https://twitter.com/skillk01) presented a session on not increasing your technical skills, but the 'soft' skills, giving back to the community and making small changes that add up over time
- HCP1934 - Upgrading to VMware vSphere 7. [Niels Hagoort](https://twitter.com/NHagoort) and [Nigel Hickey](https://twitter.com/vCenterNerd) talked about practical considerations about the upgrade from 6.5 to 6.7 and 7. This was useful to me as we are about to embark on an upgrade to 6.7
- HCP1705 - Single-Touch, Production-Ready ESXi Rollouts in Minutes with Ansible. [Bryan Sullins](https://twitter.com/RussianLitGuy) was really interesting as I have only really used Ansible for spinning up VMs from templates then OS configuration. This is a session I will be trying things in my homelab
- MAP2419 - Introduction to Infrastructure as Code. [Kyle Ruddy](https://twitter.com/kmruddy) gives a good introduction to Infrastructure as Code for vSphere Infrastructure using tools such as Terraform
- ETPD2833S - The Three V's of VMUG. [Brad Tompkins](https://twitter.com/BradTompkins_) from the VMware User Group organisation explains what VMUG is and why you **need** to be a member. Seriously go [join here](https://www.vmug.com/membership/membership-benefits) - it's free.
- HCP2050 - Certificate Management in vSphere. [Bob Plankers](https://twitter.com/plankers) dives into Certificates in vSphere. After a failed attempt to deploy certs back in v5.5 I've just never bothered again. After this session I might try again

There are so many sessions I still want to watch. I especially want to binge on the Professional Development sessions as the ones I watched were full of content to make you think beyond the technology.

### What I thought of VMworld

Certainly the sessions were great as usual. VMware made a real effort to make the event as enticing as possible with the content. I also appreciated the small things between the 'live' sessions such as the artist that painted some amazing work, the meditation sessions (first time I've ever tried that!), juggling lessons, etc. It was a fun way to spend time waiting.

Like at in person events there were opportunities for swag which I know some in IT love. In general they made the best of a bad situation. As someone that only has been to VMworld once it nice to feel included in the event this year instead of just following it via general sessions and Twitter.

I'm not sure why they had the Live sessions. They were just pre-recorded videos broadcast on a schedule. Why not just release them on demand? I know it was so it was more like a real event but real life gets in the way when you are trying to do the day job at the same time. I'm not sure many employers let people dedicate three days to VMworld.

A suggestion for VMware regarding the scheduled Live sessions - give us a downloadable ics file so we can import the session to our calendars. I know there was a 'calendar' on the site but how hard would it be to make it easy to add them to our work/personal calendars.

Another big thing at VMworld is the Hands-on Labs. This year they seemed to be just an interactive simulator that were nto a true hands-on lab. Not sure why they made this decision.

Lastly my only gripe was not really VMware's fault. Nothing beats an in person event. There were things like a Discord, various Slack channels, Twitter, etc. but as I said if you go inp person you get to dedicate the time to the event which was personally impossible.

### Wrap Up

Like anything VMware does, they do it well. Not perfect but in 2020 nothing will be. Plenty to do during the event, and lots to do after with the many sessions that are now available.

Maybe I'll see you in Barcelona next year?
