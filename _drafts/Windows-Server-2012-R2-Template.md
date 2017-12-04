---
layout: single
title: "My Windows Server 2012 R2 Template"
# date: YYYY-MM-DD
category: [Microsoft]
excerpt: "This is how I build a clean 2012 R2 Template for deployment in vSphere"
---
# Introduction

We are refreshing some VM's and I decided to document how I create a nice clean Windows Server 2012 R2 VM that I can template in vCenter.

The requirements for this template are:

* Minimal software. roles or features installed. Keep it as generic as possible
* No SMB1 installed
* Snapshotted before and after Sysprep so I can roll back and update with latest patches
* Scanned with Qualsys so no vulnerabilities found
* VMXNET3 and Paravirtual SCSI devices

## VM Creation
Create VM (sizing)
Delete extra hardware
Boot to BIOS and remove legacy hardware

## OS Installation
Add PVSCI Driver for HDD
VMware Tools

## OS Configuration

* Turn on Windows Error Reporting and send detailed reports
* Set the time zone
* Hide Server Manager
* Kick off a patch scan
* Change Resolution to 1280 x 800
* Create C:\Temp and C:\Scripts
* Change Folder options
* Show all tray icons and turn off speaker
* Lock taskbar and user small taskbar icons. Uncheck Show Windows Store apps on the taskbar
* Show Start on the display I'm using
* Turn on Smartscreen
* Add Computer and Network to desktop
* Update PwoerShell to 5.1
* Setup PowerShell
* Change Control Panel to small icons
* Change power options to High Performance and never turn off the display
* Sound scheme to No Sounds
* Install 7-Zip and set associations
* Run reg files
* Disable indexing on C:\
* Rename C:\ to OS
* unattend.xml
* Rename Ethernet0 to Ethernet
* Cleanup
* Activate windows

## Sysprep

## Templating

