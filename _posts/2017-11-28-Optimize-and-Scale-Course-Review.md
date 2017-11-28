---
layout: single
title: "VMware vSphere: Optimize and Scale [V6] - On Demand Review"
date: 2017-11-28
category: [VMware]
excerpt: "I recently took the O&S Course - On Demand. Here is my review"
---
I recently was able to take the VMware vSphere: Optimize and Scale [V6] - On Demand course from VMware. Why On Demand and not in a Classroom format? Simple - travel time and costs. I was actually looking for the Design & Deploy Fast Track course but annoyingly it seem to be scheduled very infrequently and only in London. With family and work commitments taking a week out to attend was pretty impossible.

So I started looking at the On Demand option. I was scheduled to take the VCAP6-DCV Deploy exam so the O&S On Demand course seemed like a good fit. This was my first time trying an On Demand course instead of Instructor led in class training. The interface is based on the [Hands on Labs](http://labs.hol.vmware.com/HOL/catalogs/catalog/681) so if you are familiar with that you will be comfortable using it. The modules covered were:

1. vSphere Security
2. VMware Management Resources
3. Performance in a Virtualized Environment
4. Network Scalability
5. Network Optimization
6. Storage Scalability
7. Storage Optimization
8. CPU Optimization
9. Memory Optimization
10. Virtual Machine and Cluster Optimization
11. Host and Management Scalability

First the good points. I learnt a lot. The course gave me a really grounding on using tools such as the Performance charts, esxtop, and other command line tools. I especially found the troubleshooting parts very helpful and something I will definitely use in my role. We are not licensed for Enterprise Plus so no Auto Deploy which was a problem for my VCAP6 Deploy exam. I came out the course with a good understanding of it and successfully replicated it all in my lab.

I also liked that there you were tested during the course. It's been a while since I did the iCM course in the classroom and there was no testing involved. You could just sail through the course, not take anything in and still get the certificate of completion. You were tested at the end of each module plus an overall test at the end. This had a certain percentage to pass and you only have five attempts to do it.

The material was pretty comprehensive and detailed. It consists of a manual, the video lectures, and the slides. There were clear objectives for all modules, a review of those objectives, key points, a lab and test.

There were some not so good points to the experience of the on demand course. A small point but there were a few spelling mistakes in the material. For instance:

> It is recommended that you use a Static port ***binging*** with Elastic port allocation

Maybe a nitpick but the course is not cheap...

Secondly there were inconsistencies between the manual and the actual lab environment provided. For instance at one point you had to create a new datastore from a local disk. When going through the wizard you were supposed to select the entire disk, but when you got to the partition configuration screen there was no free space on the disk you were supposed to use.

In another lab step you had to connect to the one of the vCenters. However it specifically said to verify vc01.vclass.local was vCenter listed in the PowerCLI output, but that vCenter didn't exist in the lab.

The slides were supplied in a format that you couldn't simply export from the environment. I wanted a copy for reviewing later and the only way I could get them was to print to PDF. Annoyingly they would not output in a searchable format. Can we just get a copy of the slides please VMware?

Lastly a tip. Sometimes when I was starting my lab, especially if it timed out, when starting back up it would just hang on Checking Status. After speaking to support I found out the fix is to clear your browser cache and the lab started up. Annoying!

Would I do an On Demand course again? Probably not. There is too much value in being able to speak to the instructor and other people in the class. However I do see value in On Demand for being able to go back for 30 days and review the material again as many times as you like. This would be helpful for people that don't have a lab and can't practice in production at work.

To view all VMware courses available visit the [VMware Education](https://mylearn.vmware.com/mgrreg/index.cfm) site.