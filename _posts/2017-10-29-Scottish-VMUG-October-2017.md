---
layout: single
title: "Scottish VMUG - 26th October 2017"
date: 2017-10-29
category: [VMware,VMUG]
excerpt: "Recap of the October 2017 Scottish VMUG"
---
On Thursday 26th October 2017 the second Scotland VMUG of the year happened. An amazing event!

There were 140 registered and I am sure there was well over 100 actually attended. Numbers seem to go from strength to strength and I am sure it's due to the hard work the leaders do in securing great speakers and content. Three VCDX's in one day? Yes please.

First up was [Duncan Epping](https://twitter.com/DuncanYB) on "Evolution of vSAN". First up he detailed the development of the product - 50x growth predicted from 2010 - 2020. Pretty impressive. We then saw some upcoming features in vSAN. These included:

* Live view of disks in the server
* LED blink on and off
* Simplified cluster creation
* Proactive support - vSAN calls home to VMware and GSS can call you
* Snapshots **will** be better
* 1 - 2 minute VM recovery

The biggest thing mentioned for me was full stack upgrades coming. Using VUM you can perform complete firmware and driver updates of the entire server stack from BIOS up. This will certainly help me push vSAN over another product.  Thanks Duncan!

Next up for me was the main event. Two years of work and [Chris Wahl](https://twitter.com/ChrisWahl) was with us in Scotland. The talk was "My Journey to Full Stack Engineering" and I certainly see Chris as a role model for Full Stack Engineering. He led us through his early career and how it led up to his VCDX. After speaking to some people at the vBeers something that struck people was Chris talking about getting too comfortable in his job and deciding to make a change. I think we can all relate to the fact it's gets easy to coast through your job.

Some practical advice he gave us Ops people was:

* We need to treat Ops more like Dev
* Learn a Distributed Version Control System such as GitHub, Bitbucket, GitLab, etc.
* Automate things

Specifically to GitHub:

* Setup a repository
* Contribute to a project - this can be something simple like documentation
* Build something with others

In this age GitHub and the contributions you make there can be the new CV and act as a showcase for your skills.

Chris was a very entertaining and informative speaker and I heard great feedback from people. Thanks to [Rubrik](https://www.rubrik.com/) who took Chris to Scotland.

The last VCDX up was [Rawlinson Rivera](https://twitter.com/PunchingClouds) who talked about "Breaking storage silos, quick vCloud Foundation recovery and other magic". Some of the things we saw seemed like magic - a complete Data Center recover in under 20 minutes! I did laugh at first at his statement was 'Humans are the biggest risk in infrastructure' but he is right - we all mess up at some point and when you are dealing with a DR situation that is not the time you want human error. The Cohesity product uses Policy Based Administration to eliminate the risk of humans. Looks like a great product.

I attended the Silver Sponsor [Softcat](https://www.softcat.com/) session titled "Insider Threat: Building a zero trust infrastructure". This was a really informative session for me. For me this showed the VMUG is not just for VMware Ops people as some might think. The speaker, [Adam Louca](https://twitter.com/adamlouca), didn't specifically talk about a product but more fundamental things which was technology agnostic.

I agreed with him that security is fundamentally broken and good design beats any security bolt on products. I had never though of his idea of using a load balancer as the presentation layer for **all** applications. This lets you isolate and protect those crown jewels. Also highlighted was micro segmentation of your servers and using basics such as VLANS. A flat Layer 2 network is not a good idea people. Some other tips were:

* Always assume endpoints are compromised
* Use Role Based Access Control for network access
* Model and understand the attack flow from user to application
* Implement strong boundaries between users and services
* Isolate services from each other
* Log and monitor for failed access attempts and investigate

After that was a community session by [Steven Soave](https://twitter.com/StevenSoave) on IBM Spectrum Protect for Virtual Environments. This was his first session and even though he had doubts he would be able to fill the allotted time he didn't need to worry. He came across as very knowledgeable on the product and had lots of valuable insights for users of it. Great job Steven and sure this won't be your last one.

Last session of the day was [Katarina Wagnerova](https://twitter.com/_KatkaW_) with "#Katatsea: Delivering IT on a boat". This was a really interesting talk on how Katarina worked on research vessel in the Black Sea for a month. It sounded like she had some 'unique' challenges but I came away impressed how she was always looking for a better way of providing services to the scientists on board even if the challenges of the network ultimately thwarted her (it's always the network). We always thing decent internet is available, but in this case Kat proved it's not.

At the wrap up the leaders found a very entertaining way of involving us on winning some prizes which was a lot of fun. I think it's a testament to [Sandy Bryce](https://twitter.com/sandybryce), [Chris Storrie](https://twitter.com/chrisstorrie), [James Cruickshank](https://twitter.com/vCrooky) and [Iain Balmer](https://twitter.com/Balmeri) that they continually are looking for new ways to engage with the community. Thanks guys! We all owe them a lot for their work they put into the VMUG.

Also Chris, you are the sticker master!

![VMUG Stickers]({{ site.url }}/assets/images/VMUG-Stickers.png)

After all that was the vBeers. Again I found it incredibly valuable. I met lots of new people and had great conversations including VCAP exam tips, Change Control, the realisation we all have the same challenges no matter what size the organisation, Jager Bombs, VCDX versus vExpert, asking why things are done the way they are, programming Alexa, hangovers, working at the coal face of IT and the career path from postman to IT operations.

I love speaking to people about these sort of random topics and hearing what the community is thinking/dealing with/being challenged with. I know I find it reassuring I am facing the same problems as others. Also for some people it's good to have a little vent amongst friendly faces!

Finally which other VMUG in the world can you go to and get a wee trim at the same time as you are having a beer?

![vBeers Trim]({{ site.url }}/assets/images/vBeers-Trim.png)

Next VMUG is pencilled in for Thursday 26th April 2018 in Glasgow. I intend to spread the word at other user groups I attend and get some new faces along. Already looking forward to it!