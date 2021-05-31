---
layout: single
classes: wide
title: "vSphere DSC Test-VmwDscConfiguration Error"
date: 2021-05-31
category: [VMware]
excerpt: "Finding a bug in the new vSphere DSC release specific to one cmdlet in PowerShell 5.1"
---
## Introduction

With the recent release of [vSphere DSC 2.2](https://blogs.vmware.com/PowerCLI/2021/02/vspheredsc.html) it has introduced the capability to use DSC without using a Windows machine to apply the configuration. I started investigating using this new method for applying DSC configurations against a vSphere environment and hit an issue at the Test phase of my configuration testing. I thought I would document the issue and simple workaround here.

At the time of this blog post I was using:

- vSphere DSC v2.2.0.84
- VMware PS Desired State Configuration module v1.0.0.16
- PowerCLI v12.3.0.17860403
- PowerShell v5.1
- Windows 10
- vCenter Server 7.0 Update 2b

If you want a guide on vSphereDSC see my earlier blog post series [vSphere DSC - Part 1]({{ site.baseurl }}{% post_url 2019-08-26-vSphereDSC-Part-1 %}).

## The DSC Configuration

I created a very simple configuration to create a Datacenter called 'vGemba-DC' on my VCSA:

```powershell
$Credential = Import-CliXml -path C:\Creds\admin.xml

Configuration Datacenter_Config {
    Import-DscResource -ModuleName VMware.vSphereDSC

    Node localhost {
        Datacenter vGemba-Datacenter {
            Server = 'vcsa01.corp.contoso.com'
            Credential = $Credential
            Name = 'vGemba-DC'
            Location = ''
            Ensure = 'Present'
        }
    }
}
```

You can see it is pretty simple. I am pulling credentials from an encrypted xml file as I described in a previous blog post [Saving PowerCLI Credentials]({{ site.baseurl }}{% post_url 2019-05-19-Saving-PowerCLI-Credentials %}). As it's my lab this is a way of me having to repeatedly enter credentials.

Next I am using the DSC Resource [Datacenter](https://github.com/vmware/dscr-for-vmware/blob/master/Documentation/DSCResources/Datacenter/Datacenter.md) to ensure there is a datacenter called  `vGemba-DC` on my vCenter `vcsa01.corp.contoso.com`. Very straightforward stuff to create and make sure there is a datacenter. I have saved this file under the name `new-vmwdc.ps1`.

## Creating the configuration object

Before this release of vSphereDSC your only choice was to create the mof file on a Windows computer and then apply that mof to the vSphere environment. Now with the module `VMware.PSDesiredStateConfiguration` installed we can create an object which contains the details of the configuration:

```powershell
PS C:\Repos\vsphere-dsc> $dscconfig = New-VmwDscConfiguration -Path .\new-vmwdc.ps1
```

Now we can check the object was created correctly as nothing is returned:

```powershell
PS C:\repos\vsphere-dsc> $dscconfig

Nodes       InstanceName
-----       ------------
{localhost} Datacenter_Config
```

## Test DSC Compliance - The Error

To check, I start a PowerCLI session to my vCenter and check which datacenter's are present:

```powershell
PS C:\repos\vsphere-dsc> Get-Datacenter

Name
----
DC01
````

So the desired datacenter called `vGemba-DC` is not there. To check now using DSC we use the cmdlet [Test-VmwDscConfiguration](https://github.com/vmware/dscr-for-vmware/blob/master/VMware.PSDesiredStateConfiguration.md#start-test-and-get-vmwdscconfiguration). This of this as the equivalent of `Test-DscConfiguration` in 'traditional' DSC:

```powershell
PS C:\Repos\vsphere-dsc> Test-VmwDscConfiguration -Configuration $dscconfig
Select-Object : Property "InvokeResult" cannot be found.
At C:\Program Files\WindowsPowerShell\Modules\VMware.PSDesiredStateConfiguration\1.0.0.16\Functions\Public\Test-VmwDscC
onfiguration.ps1:76 char:35
+ ...  = ($testResults | Select-Object -ExpandProperty InvokeResult | Where ...
+                        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (System.Collections.Hashtable:PSObject) [Select-Object], PSArgumentExce
   ption
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

True
```

There is the error which is the point of this post. When I hit this bug I decided to try with the `-Detailed` parameter to see if I could get more information:

```powershell
PS C:\Repos\vsphere-dsc> Test-VmwDscConfiguration -Configuration $dscconfig -Detailed

InDesiredState ResourcesInDesiredState ResourcesNotInDesiredState     NodeName
-------------- ----------------------- --------------------------     --------
         False {}                      {Datacenter vGemba-Datacenter} localhost
```

With the `-Detailed` parameter `Test-VmwDscConfiguration` works. This seems to indicate a bug in the cmdlet/module.

## The Error

As I could narrow this down to a particular cmdlet I decided to open a bug on the [GitHub site for vSphere DSC](https://github.com/vmware/dscr-for-vmware/issues) but I discovered someone had already done this. [Bug #323](https://github.com/vmware/dscr-for-vmware/issues/323) describes the issue exactly. Seems to be specific to using this cmdlet without `-Detailed` running under PowerShell v5.1. According to the [fix](https://github.com/vmware/dscr-for-vmware/pull/324) this is due to:

> Fix bug with Test-VmwDscConfiguration cmdlet on PowerShell 5.1 that occurred due to NodeResult being PSObject instead of PSCustomObject. Select-Object -ExpandProperty InvokeResult doesn't work on PSObject on PowerShell 5.1.

So it looks like in a future release of vSphere DSC this will be fixed.

## Wrap Up

This is a PSA to others trying out the new method of invoking DSC configurations. Apart from this bug it's working well for me and I like it as it removes an additional step of creating the mof files. Go try out vSphere DSC 2.2 but just remember to use `-Detailed`. I will update this post once the fix is rolled out in a future release.