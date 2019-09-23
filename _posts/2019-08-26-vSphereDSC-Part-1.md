---
layout: single
title: "vSphere DSC - Part 1"
date: 2019-08-26
category: [VMware]
excerpt: "What is vSphere DSC? Part one will cover the basics of DSC and vSphere DSC along with some pre-requisites to get ready for Part 2"
---
## Introduction

There have been attempts in the past to create a vSphere Desired State Configuration (DSC) module but recently VMware released v2.0 of an official module for configuring a vSphere environment. I've been tinkering with DSC in a recent internal Code-A-Thon at work so was interested to see how it applied to vSphere.

This is Part 1 of 2 (for now) of blogs exploring the DSC module.

## What is Desired State Configuration?

DSC is originally a technology release by [Microsoft](https://docs.microsoft.com/en-us/powershell/dsc/overview/overview), initially with Windows Server 2012 R2. It's been around for a long time and is a mature, stable product.

DSC is a way of writing declarative code for your environment. The usually way of building a server with PowerShell would be to write out a series of commands to perform the necessary steps to reach the end goal of a server that is ready to go. However using DSC you write out the end state of the build, and let DSC figure out the steps to get there.

A good example I found on Microsoft's website was [this one](https://docs.microsoft.com/en-us/powershell/dsc/overview/dscforengineers). Say for example you needed a share created on a server. You would use the cmdlet [New-SmbShare](https://docs.microsoft.com/en-us/powershell/module/smbshare/new-smbshare):

~~~ posh
# Create a share in Windows Server
New-SmbShare -Name MyShare -Path C:\Demo\Temp -FullAccess Alice -ReadAccess Bob
~~~

Pretty easy right? But to make it make it error proof, idempotent, and good enough for production it would look something like:

~~~ posh
# But actually creating a share in an idempotent way would be

$shareExists = $false
$smbShare = Get-SmbShare -Name $Name -ErrorAction SilentlyContinue
if($smbShare -ne $null)
{
    Write-Verbose -Message "Share with name $Name exists"
    $shareExists = $true
}

if ($shareExists -eq $false)
{
    Write-Verbose "Creating share $Name to ensure it is Present"
    New-SmbShare @psboundparameters
}
else
{
    # Need to call either Set-SmbShare or *ShareAccess cmdlets
    if ($psboundparameters.ContainsKey("ChangeAccess"))
    {
       #...etc, etc, etc
    }
}
~~~

A bit more more complex and not as easy to figure out.

Using DSC you would declare you want to create a share, what access rights, description, etc. and let the operating system/PowerShell figure out what needs to be done to create what you want:

~~~ posh
# A configuration is a special kind of PowerShell function
Configuration Sample_Share
{
   Import-DscResource -Module xSmbShare

   Node $NodeName
   {
      # Next, specify one or more resource blocks
      # Resources are simply PowerShell modules that
      # implement the logic of "how" to execute a task
      xSmbShare MySMBShare
      {
          Ensure      = "Present"
          Name        = "MyShare"
          Path        = "C:\Demo\Temp"
          ReadAccess  = "Bob"
          FullAccess  = "Alice"
          Description = "This is an updated description for this share"
      }
   }
}

Sample_Share

Start-DscConfiguration Sample_Share
~~~

Now isn't that a bit simpler? What you want to happen is clearly defined in the Configuration code block. No matter how many times you run this code, you will always end up with the same result.

Microsoft have excellent DSC [help available](https://docs.microsoft.com/en-us/powershell/dsc/overview/overview) which I highly recommend reading if you are starting to look at DSC.

## vSphere Desired State Configuration

As I said earlier I am getting interested in DSC but my first love is VMware.

Back in 2016 I remember reading a [blog post](http://www.lucd.info/2016/06/04/vspheredsc-intro/) by scripting legend [Luc Dekens](https://twitter.com/LucD22) when he released a DSC module for vSphere. I remember thinking that I should try it out but never got the chance.

Then in December 2018 VMware [released an official DSC module](https://blogs.vmware.com/PowerCLI/2018/12/getting-started-dsc-for-vmware.html). As a v1.0 release it only covered a few things:

* vCenter Statistics
* vCenter Settings
* Host NTP settings
* Host DNS settings
* SATP Claim Rules
* Host Transparent Page Sharing

It struck me as a way for them to validate vSphere DSC and get something out the door.

Then in [June 2019 v2.0 was released](https://blogs.vmware.com/PowerCLI/2019/06/new-release-dsc-resources-for-vmware-2-0.html). This added a lot to the module. From the [GitHub readme](https://github.com/vmware/dscr-for-vmware/blob/master/README.md) there are now resources for:

* NTP, DNS, TPS and Syslog settings of VMHosts
* Message of the Day and Issue Advanced Settings of VMHosts
* Host services of VMHosts
* SATP Claim Rules of VMHosts
* Statistics and Advanced Settings of vCenters
* Creating, Updating and Deleting Clusters
* PowerCLI configuration

In total there are 22 resources available now. I am sure most people could use it to do perform some initial configuration of an environment. I'm hoping there is a consistent release cycle too.

## Some Pre-requisites

The first thing you need to do is have PowerShell v5.1 installed (no PowerShell Core support). Next you need PowerCLI installed (v10.1.1 minimum):

~~~ posh
# New install of PowerCLI:
Install-Module -Name VMware.PowerCLI -Scope AllUsers

## If you already have PowerCLI installed, update:
Update-Module -Name VMware.PowerCLI -Force
~~~

I write a blog post on v10 when it was released with some more information on PowerCLI lifecycle maintenance. It's worth [checking out]({{ site.baseurl }}{% post_url 2018-03-11-PowerCLI-v10 %}).

I'd also recommend installing [Visual Studio Code](https://code.visualstudio.com/download) with the [PowerShell Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) simply because it's awesome and so much nicer to work with than the ISE.

Finally we need to install the vSphere DSC module. In PowerShell (running as an admin) use the command:

~~~ posh
# Install the module
Install-Module -Name VMware.vSphereDSC

#Verify the module is installed
Get-DscResource -Module VMware.vSphereDSC

ImplementedAs   Name                      ModuleName                     Version    Properties
-------------   ----                      ----------                     -------    ----------
Composite       Cluster                   VMware.vSphereDSC              2.0.0.0    {DependsOn, PsDscRunAsCredential, Ser...
PowerShell      Datacenter                VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Location, Name...}
PowerShell      DatacenterFolder          VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Location, Name...}
PowerShell      DrsCluster                VMware.vSphereDSC              2.0.0.0    {Credential, DatacenterLocation, Data...
PowerShell      Folder                    VMware.vSphereDSC              2.0.0.0    {Credential, DatacenterLocation, Data...
PowerShell      HACluster                 VMware.vSphereDSC              2.0.0.0    {Credential, DatacenterLocation, Data...
PowerShell      PowerCLISettings          VMware.vSphereDSC              2.0.0.0    {SettingsScope, CEIPDataTransferProxy...
PowerShell      vCenterSettings           VMware.vSphereDSC              2.0.0.0    {Credential, Server, DependsOn, Event...
PowerShell      vCenterStatistics         VMware.vSphereDSC              2.0.0.0    {Credential, Period, Server, DependsO...
PowerShell      VMHostAccount             VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Id, Role...}
PowerShell      VMHostDnsSettings         VMware.vSphereDSC              2.0.0.0    {Credential, Dhcp, DomainName, HostNa...
PowerShell      VMHostNtpSettings         VMware.vSphereDSC              2.0.0.0    {Credential, Name, Server, DependsOn...}
PowerShell      VMHostSatpClaimRule       VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, RuleName...}
PowerShell      VMHostService             VMware.vSphereDSC              2.0.0.0    {Credential, Key, Name, Server...}
PowerShell      VMHostSettings            VMware.vSphereDSC              2.0.0.0    {Credential, Name, Server, DependsOn...}
PowerShell      VMHostSyslog              VMware.vSphereDSC              2.0.0.0    {Credential, Name, Server, CheckSslCe...
PowerShell      VMHostTpsSettings         VMware.vSphereDSC              2.0.0.0    {Credential, Name, Server, DependsOn...}
PowerShell      VMHostVss                 VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, Server...}
PowerShell      VMHostVssBridge           VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, Server...}
PowerShell      VMHostVssSecurity         VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, Server...}
PowerShell      VMHostVssShaping          VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, Server...}
PowerShell      VMHostVssTeaming          VMware.vSphereDSC              2.0.0.0    {Credential, Ensure, Name, Server...}
~~~

## Wrap Up

We are ready to go. PowerCLI is up to date, we have the DSC resource installed, and an IDE to write code.

In [vSphere DSC - Part 2]({{ site.baseurl }}{% post_url 2019-09-23-vSphereDSC-Part-2 %}) we will have a look at some modules to configure a vSphere Cluster and hosts.
