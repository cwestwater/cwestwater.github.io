---
layout: single
title: "Ubiquiti Unifi Home Network"
date: 2018-04-03
category: [Review]
excerpt: "My Unifi home network hardware review"
---
# Introduction

I was sick of consumer network hardware. I have lost count of how many routers I have gone through over the years. I seemed to have to replace the router every couple of years. A great example of this was my [TP-Link N600](https://www.tp-link.com/uk/products/details/TD-W9980.html) router. It was great at first - rock solid Internet and the WiFi range was adequate. Then after a 18 months I started getting complaints of the WiFi dropping out from my wife. Once that happened it was Defcon 1! The dropouts were random and I could not find a cause. I was about to buy another router when I stumbled upon some people on Twitter talking about the Unifi WiFi system. After reading up about it I decided to give it a go.

## Phase 1 - UniFi AP-AC-LR Access Point

As the issue seemed to be just Wifi drop outs I bought a [UniFi AP-AC-LR](https://www.ubnt.com/unifi/unifi-ap-ac-lr/) access point. The key to the UniFi system is that you use controller software to provision and configure the hardware. As this was an experiment I bought from [Broadband Buyer.com](https://www.broadbandbuyer.com/products/23778-ubiquiti-uap-ac-lr-cloud/) as they give you a free three year hosted cloud controller. I disabled the WiFi on the router and plugged in the AP.

I immediately saw improved WiFi performance. With the single AP in the house I get coverage across the whole house, garden and garage. I can actually go over to the next street and still be connected to the WiFi. We have experienced zero WiFi issues since it was installed.

## Phase 2 - UniFi US-8 Switch

After installing the AP I got the UniFi bug. I had an unmanaged Netgear switch in the office to connect my desktop, work laptop, Synology NAS, etc. I upgraded the Synology to a [DS1517+](https://www.synology.com/products/DS1517+) which comes with four gigabit LAN ports. To take advantage of bonding them into an LACP team I decided to get a [UniFi US-8 switch](https://www.ubnt.com/unifi-switching/unifi-switch-8/). This let me connect all my office hardware plus it has the benefit of a PoE Passthrough port so I could power the AP using PoE. LACP was easy to setup on both the Synology and the switch.

## Phase 3 - Unifi Cloud Key, Unifi USG Router and Draytek Vigor 130 Modem

Pleased with the hardware so far I decided to ditch the old TP-Link router. I looked at the [UniFi USG Security Gateway](https://www.ubnt.com/unifi-routing/usg/) as it would complement the exiting hardware. I could have used the TP-Link as the modem part but in the end decided to buy a [Draytek Vigor 130](http://www.draytek.co.uk/products/business/vigor-130). The Vigor 130 was pretty much plug and play. It defaulted to Bridge mode so it passes the authentication of my BT Infinity connection to the USG router.

I plugged the LAN connection from the Vigor to the WAN port on the USG. I then connected a laptop to the LAN1 port on the USG. The laptop gets a DHCP IP and you browse to the default gateway which is the USG. All I had to do was select a PPPoE connection and enter a username of `bthomehub@btbroadband.com` and password of `bt`. I was connected to the internet. Easy! I then connected the LAN1 port to my home network.

As I mentioned above the key to the UniFi system is the controller. I had a hosted solution but I decided to splurge on the hardware based [UniFi Cloud Key](https://www.ubnt.com/unifi/unifi-cloud-key/). I didn't really need it as you can run the controller on Windows, macOS, Linux, in the cloud, even as a container on my Synology using Docker. I like my gadgets so I bought the Cloud Key device.

## The UniFi Controller

The controller is the command and control for all the UniFi devices. Using the controller you gain:

* GUI based management of all UniFi devices
* Multiple Dashboards
* Detailed Analytics of clients, traffic and devices on the network
* Access to controller both inside the network and outside via the Web or an App on your phone

The amount of configuration and features that are available are pretty incredible:

* Guest Wifi network with features such as a guest sign in portal, voucher authentication, etc.
* Up to four WiFi networks
* Intrusion Detection System/Intrusion Prevention System
* Deep Packet Inspection
* RADIUS Authentication
* Wide range of Firewall options
* GeoIP Filtering
* Profiles which let you schedule device access to the internet
* 802.1X Control
* A massive range of alerting options
* Cloud access to controller
* Automated backups of controller settings
* Automatic speed tests

and a whole lot more.

## Benefits

What I really love about the system is having deep visibility into the network. When you connect to the controller there is an initial dashboard presented:

![UniFi Dashboard]({{ site.url }}/assets/images/UniFi-Dashboard.png)

You can see here the current throughput and latency, WiFi channels in use, the number of clients and Deep Packet Inspection on the traffic.

You can then look at each client on the network and see how it is connected and some details on it such as usage, connection time, IP address, etc.

![UniFi Clients]({{ site.url }}/assets/images/UniFi-Clients.png)

You can also dive into the types of traffic on the network (called Deep Packet Inspection):

![UniFi DPI]({{ site.url }}/assets/images/UniFi-DPI.png)

You can see here what is going on with the traffic.

Of course there is an app available on Android and IOS that gives all the functionality of the browser version:

![UniFi App]({{ site.url }}/assets/images/UniFi-App.png)

## Helpful Links

I found a helpful [YouTube video](https://www.youtube.com/watch?v=7GupmjooP9Q) by James Smith that ran through the setup of the modem and USG.

There were a couple of things I needed to do to get the setup I wanted. The first was being able to access the GUI of the Vigor modem from the LAN. [This post](https://owennelson.co.uk/accessing-a-modems-web-interface-through-a-ubiquiti-usg/) from Owen Nelson sorted that out.

Finally I use a YouView set top box for TV in the house. This is heavily dependent on Multicast and setting up IGMP correctly. [This post](https://community.bt.com/t5/Home-setup-Wi-Fi-network/Step-by-Step-guide-to-configuring-Unifi-Security-Gateway-USG-to/td-p/1796166) from kleen fixed the YouView streaming problems I experienced. I put a copy of the [config.gatway.json](https://github.com/cwestwater/Unifi/blob/master/config.gateway.json) file that I use on GitHub if anyone wants to resolve the same issue.

## Conclusion

The UniFi range of network products seem to be very popular amongst the IT Community and now I am a convert I can see why. The range of settings, the customisation and performance of the solution is fantastic. The [community forum](https://community.ubnt.com/) is very active and Ubiquiti have an [active roadmap](https://community.ubnt.com/t5/UniFi-Routing-Switching/USG-Feature-Roadmap-January-2017-update/td-p/1792230) of features. Too often with consumer grade network hardware you maybe get one firmware upgrade in it's lifetime then the device goes end of life.

I am confident that I will not get complaints about internet access from my family again.