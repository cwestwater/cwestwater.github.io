---
layout: single
title: "pfSense in VMware Workstation"
date: 2018-06-10
category: [VMware]
excerpt: "I use pfSense in my nested lab. How do I setup and use it?"
---
# Introduction

My lab is completely nested in VMware Workstation v14 and I use [pfSense](https://www.pfsense.org/) to isolate the various labs I run. In this blog post I am going to run through how to set it up as a VM in Workstation and then set it up to isolate some nested VM's.

pfSense acts a virtual router/firewall that lets me run whatever I want such as AD, DHCP, vCenter, etc. behind it without affecting my home network. The list of [features](https://www.pfsense.org/about-pfsense/features.html) that pfSense can provide are extensive. In this blog post I am using v2.4.3 of pfSense.

## Download pfSense

pfSense is available for free with paid support options available. [Download the Community Edition](https://www.pfsense.org/download/) picking the following options from the drop downs:

* Version: 2.4.3
* Architecture: AMD64 (64-bit)
* Installer: CD Image (ISO) Installer

Then choose the Mirror closest to you. The download is a gz archive file so you need to use something like [7-Zip](https://www.7-zip.org/download.html) to extract it to get the ISO.

## Create VM in Workstation

Next up is to create the pfSense VM in Workstation:

1. Go to `File...New Virtual Machine`
2. Choose `Custom (advanced)`
3. Select the Virtual machine hardware compatibility that is right for your version of Workstation
4. Pick `I will install the operating system later`
5. Guest operating system is `Other` then in the drop down select `FreeBSD 11 64-bit`
6. Enter the VM name and select the location
7. 1 CPU and 1 core per processor is fine
8. 265MB of memory is fine
9. Choose either `Bridged` or `NAT` network type depending on your preference
10. Use a `LSI Logic` SCSI Controller
11. Use an `IDE` virtual disk type
12. Create a new virtual disk
13. Maximum size of `5GB` and choose `Store virtual disk as a single file`
14. Leave the disk name as default
15. Click `Finish`

Now Edit the virtual machine settings. Make the following changed to the Hardware:

* Remove `USB Controller`
* Remove `Sound Card`
* `CD/DVD (IDE)` connect to the pfSense ISO image file that you downloaded. Ensure `Connect at power on` is checked

Now this is something I do for all my VM's. By default you have legacy hardware such as Serial and Parallel Ports so I like to get rid of them:

1. Right click your pfSense VM and select `Power...Power On to Firmware`
2. Move to `Advanced` and select `I/O Device Configuration`
3. For `Serial port A`, `Serial port B`, `Parallel port` and `Floppy disk controller` press Space until each shows as `Disabled`
4. Press `F10` to Save and Exit

## Initial Installation of pfSense

The VM will now boot. We can go ahead and start the initial install of pfSense.

1. Press `Enter` to Accept the copyright notice
2. Choose `Install` and choose `OK`
3. Select the keymap that matches your keyboard
4. In the Partitioning options choose `Auto (UFS)`
5. pfSense will now install
6. At the end it will ask if you want to do any Manual Configuration. Choose `No`
7. `Reboot` to complete the installation

## Initial Configuration

Once the VM reboots, pfSense will start and will start an initial configuration:

1. `Should VLANS be set up now [y¦n]?` choose `n`
2. Enter `em0` as the WAN interface
3. For the LAN interface just press `Enter`
4. A summary of the config is displayed: `WAN -> em0`. Choose `y`

pfSense will start and present the main menu. You can test connectivity to the outside world by selecting option 7 and entering a host name such as www.google.com or IP 8.8.8.8. A ping response should be displayed.

## LAN interface

Ok so far we have a basic config. pfSense has a WAN interface and can communicate with the outside world. Now we need to setup the LAN interface. This is the interface your lab will use as the default gateway to communicate out.

I use a LAN segments to isolate each lab environment. So I connect the second NIC on the VM to the LAN segment setup for the lab. This is the LAN interface in pfSense.

1. Select option `6) Halt System` and press `y` to proceed
2. Once the VM is shutdown edit the VM settings
3. Remove the `CD/DVD (IDE)` as we don't need it any more
4. Add an `Ethernet Adapter` and connect it to the LAN segment your lab will use
5. Power on the VM
6. Once booted select option `1) Assign Interfaces`
7. `Should VLANS be set up now [y¦n]?` choose `n`
8. Enter `em0` as the WAN interface
9. Enter `em1` as the LAN interface
10. Confirm `WAN -> em0` and `LAN -> em1`. Press `y` to proceed

Now to set the IP address of the LAN interface. This will be your default gateway for the lab:

1. Select option `2) Assign interface(s) IP address` and press `y` to proceed
2. Select option `2` for `2 - LAN (em1)`
3. Enter the LAN IPv4 Address
4. Type in the subnet mask
5. Press `Enter` for none
6. Press `Enter` for none as I don't use IPv6 in the LAN
7. Type `n` if you don't want pfSense to be a DHCP server. I typically use AD DHCP
8. Type `y` to revert to HTTP as the webConfigurator protocol
9. Press `Enter` to complete

That is pfSense setup enough to act as a router for your nested lab. However some further setup is needed using the web console.

## Final Configuration

Come final configuration is needed such as setting hostname, DNS, etc. More importantly we should install `Open-VM-Tool` that is the correct set of VMware Tools,

To access the web console you need to connect a VM to the LAN Segment used for the LAN interface and set an appropriate IP address on the subnet. Make sure you can ping the pfSense LAN IP address and start a web browser.

1. Login to the web console using the default username and password of `admin` and `pfsense`
2. You can follow the configuration wizard but I typically skip it by clicking on the pfSense logon at the top left
3. Go to `System...General Setup`
4. Enter suitable values for `Hostname`, `Domain`, `DNS Servers` and `Timezone`. Click `Save` when done
5. Go to `System...Package Manager`
6. Click on `Available Packages` and in the search box find `Open-VM-Tools`
7. Click `Install` next to the `Open-VM-Tools` package
8. Go to `System...Update` and ensure you are running the latest version of pfSense. If required update
9. Go to `System...User Manager` and click the Edit icon next to the user `admin`. Change the `Password` from the default
10. Finally go to `Diagnostics...Reboot` and click `Reboot`

## Wrap up

A lengthy set of instructions but once you have done it a couple of times pfSense is very quick to setup for your needs. You can do further things such as setting up a DMZ, firewall rules, multiple LAN interfaces to segment traffic, etc.

pfSense is a valuable tool in your home lab setup.