---
layout: single
title: "Microsoft Security Compliance Toolkit"
date: 2018-08-05
category: [Microsoft]
excerpt: "Overview of the Microsoft Security Compliance Toolkit 1.0 which can greatly increase your security posture"
---
# Introduction

I am currently planning a re-organization of our AD structure. Due to lots of hands in the mix, acquisitions, divestitures, etc. it's not optimal. There are lots of GPO's that are not linked anywhere, setting for for OS's, well you get the picture.

Our AD layout was designed to make objects easy to find but not easy to apply GPO's to the appropriate objects that should be in scope. The opposite of how it should be! One of the primary goals is to make sure there are GPO's that apply a good set of security principles to computer and user objects.

Microsoft has published the [Microsoft Security Compliance Toolkit](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-compliance-toolkit-10) (SCT) that provides a collection of tools to apply security baselines for Windows and Office. From the download page Microsoft's overview is:

> The Microsoft Security Configuration Toolkit enables enterprise security administrators to effectively manage their enterprise’s Group Policy Objects (GPOs).  Using the toolkit, administrators can compare their current GPOs with Microsoft-recommended GPO baselines or other baselines, edit them, store them in GPO backup file format, and apply them via a domain controller or inject them directly into testbed hosts to test their effects.

## What's in the Toolkit

From the documentation on the Microsoft Docs website, the SCT contains:

* Windows 10 Security Baselines
  * Windows 10 Version 1803 (April 2018 Update)
  * Windows 10 Version 1709 (Fall Creators Update)
  * Windows 10 Version 1703 (Creators Update)
  * Windows 10 Version 1607 (Anniversary Update)
  * Windows 10 Version 1511 (November Update)
  * Windows 10 Version 1507
* Windows Server Security Baselines
  * Windows Server 2016
  * Windows Server 2012 R2
* Microsoft Office Security Baseline
  * Office 2016
* Tools
  * Policy Analyzer Tool
  * Local Group Policy Object (LGPO) tool

You can see there is quite a lot there. The most useful thing I am using is the security baselines.

## The Security Baselines

When you download the Toolkit it comes as a series of zip files. I am going to pick on the Windows 10 Version 1803 Security Baseline. Once extracted you get a series of folders:

* Documentation
* GP Reports
* GPOs
* Local_Script
* Templates
* WMI Filters

There are a number of GPOs included in the baseline:

* MSFT Internet Explorer 11 - Computer
* MSFT Internet Explorer 11 - User
* MSFT Windows 10 and Server 2016 - Credential Guard
* MSFT Windows 10 and Server 2016 - Defender Antivirus
* MSFT Windows 10 and Server 2016 - Domain Security
* MSFT Windows 10 RS4 - BitLocker
* MSFT Windows 10 RS4 - Computer
* MSFT Windows 10 RS4 - User

Lets have a look what is in the policy MSFT Windows 10 RS4 - Computer policy:

![Computer Policy]({{ site.url }}/assets/images/SCT-Computer-GPO.png)

It's quite a list! Expanding out the sections it applies setting such as the Firewall, Audit policies, Event log sizing, SmartScreen, User Rights Assignment, Security Options, etc. What I also like there are settings to disable all the consumer nonsense that is part of Windows 10, such as Windows Game Recording, Microsoft Consumer Experiences, Xbox Live, and others.

I recommend you download the baselines and have a look through what is included. I would *not* recommend blindly applying the policies without verifying each setting and checking it's applicable for your environment. The Documentation folder has an Excel sheet that details all the settings in the GPOs that is easier to search through than the HTML reports in the GP Results folder.

You can see that there are baselines for each version of Windows 10 and handily in the Documentation folder there is an Excel sheet that lists the differences in the GPO from the previous version of Windows 10.

What is also nice is that included in the download is a set of WMI filters. This means you can import them and easily target the appropriate operating systems or applications for the GPOs.

## Policy Analyzer Utility

Included is a tool called Policy Analyzer. This allows you to compare a set of GPOs and show where there are issues or differences between them. You can also compare a GPO against what the Local Policy and Registry is set too. Another excellent feature is that you can get a baseline of a system and then at a future point see what if anything has changed.

I have not used this properly yet but once I start building out the new OUs and GPOs I will be making use of this tool, especially to baseline.

## Local Group Policy Object Utility

The final tool is the Local Group Policy Object (LGPO) Utility. This is a command line tool that targets operations in the Local Group Policy. You can load and apply settings from Registry Policy files, templates, and LGPO text files.

As we are using AD to push the polices I won't use it, but I can see value if you are running a DMZ where the OS is not domain joined.

## Wrap Up

I highly recommend you go look at the Security Compliance Toolkit. It can greatly improve your computer and user object security posture in Active Directory. Even if you don't apply them directly it can give you a good idea on policies that can help you.

Download the SCT [here](https://www.microsoft.com/en-us/download/details.aspx?id=55319).