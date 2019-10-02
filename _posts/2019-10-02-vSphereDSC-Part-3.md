---
layout: single
title: "vSphere DSC - Part 3"
date: 2019-10-02
category: [VMware]
excerpt: "This is Part 3 of a series on vSphere DSC. We will cover a simple vCenter DSC example"
---
## Introduction

This is Part 3 of a series of posts on vSphere DSC. If you have not read them go back and check out [vSphere DSC - Part 1]({{ site.baseurl }}{% post_url 2019-08-26-vSphereDSC-Part-1 %}) and [vSphere DSC - Part 2]({{ site.baseurl }}{% post_url 2019-09-23-vSphereDSC-Part-2 %}).

We covered a simple ESXi example in Part 2 so lets look some vCenter examples.

## Datacenter Setup

So one of the first things you do with a fresh vCenter is add a [Datacenter](https://github.com/vmware/dscr-for-vmware/blob/master/Documentation/DSCResources/Datacenter/Datacenter.md). This is very straightforward in vSphere DSC:

~~~ posh
$server = 'vcsa1.corp.contoso.com'

$configurationData = @{
    AllNodes = @(
        @{
            NodeName = 'localhost'
            PSDscAllowPlainTextPassword = $true
            PSDscAllowDomainUser = $true
        }
    )
}

Configuration DatacenterSetup {
    Import-DscResource -ModuleName VMware.vSphereDSC

    $Credential = Import-Clixml C:\Credential\a-cwestwater.cred

    Node localhost {
        Datacenter vCenterDatacenter {
            Server = $Server
            Credential = $Credential
            Name = 'vGemba'
            Location = ''
            Ensure = 'Absent'
        }
    }
}

DatacenterSetup -ConfigurationData $configurationData
~~~

One thing you might notice immediately under `ConfigurationData` is the addition of `PSDscAllowDomainUser = $true`. This keyword allows you to use an AD account to authenticate with vCenter like you normally do through PowerCLI or the GUI.

Next we define the Configuration `DatacenterSetup` and import the DSC Resource and a saved AD credential. Now onto the `Datacenter` resource called `vCenterDatacenter`:

* `Server` is the vCenter we are creating the datacenter in
* `Credential` is the saved credential
* `Name` is the name of the datacenter object being created
* `Location` is where in the hierarchy the object should be placed. In this case the root - `''`
* `Ensure` the datacenter is in vCenter

First run a `Test-DscConfiguration`:

~~~ posh
PS C:\Scripts\vSphereDSC> Test-DscConfiguration -Path .\DatacenterSetup

PSComputerName  ResourcesInDesiredState        ResourcesNotInDesiredState     InDesiredState
--------------  -----------------------        --------------------------     --------------
localhost                                      {[Datacenter]vCenterDatacen... False
~~~

You can see we are not in the desired state. Let's fix it:

~~~ posh
PS C:\Scripts\vSphereDSC> Start-DscConfiguration -Path .\DatacenterSetup -Wait

PS C:\Scripts\vSphereDSC> Start-DscConfiguration -Path .\DatacenterSetup -Wait -Verbose
VERBOSE: Perform operation 'Invoke CimMethod' with following parameters, ''methodName' = SendConfigurationApply,'className' = MSFT_DSCLocalConfigurationManager,'namespaceName' = root/Microsof
t/Windows/DesiredStateConfiguration'.
VERBOSE: An LCM method call arrived from computer IT1 with user sid S-1-5-21-2027855393-2688064357-1577006696-1107.
VERBOSE: [IT1]: LCM:  [ Start  Set      ]
VERBOSE: [IT1]: LCM:  [ Start  Resource ]  [[Datacenter]vCenterDatacenter]
VERBOSE: [IT1]: LCM:  [ Start  Test     ]  [[Datacenter]vCenterDatacenter]
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-View	Finished execution
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-Inventory	Finished execution
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-Datacenter	Finished execution
VERBOSE: [IT1]: LCM:  [ End    Test     ]  [[Datacenter]vCenterDatacenter]  in 4.9220 seconds.
VERBOSE: [IT1]: LCM:  [ Start  Set      ]  [[Datacenter]vCenterDatacenter]
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-View	Finished execution
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-Inventory	Finished execution
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:30	Get-Datacenter	Finished execution
VERBOSE: [IT1]:                            [[Datacenter]vCenterDatacenter] 01/10/2019 14:16:31	New-Datacenter	Finished execution
VERBOSE: [IT1]: LCM:  [ End    Set      ]  [[Datacenter]vCenterDatacenter]  in 0.2340 seconds.
VERBOSE: [IT1]: LCM:  [ End    Resource ]  [[Datacenter]vCenterDatacenter]
VERBOSE: [IT1]: LCM:  [ End    Set      ]
VERBOSE: [IT1]: LCM:  [ End    Set      ]    in  5.5390 seconds.
VERBOSE: Operation 'Invoke CimMethod' complete.
VERBOSE: Time taken for configuration job to complete is 5.632 seconds
~~~

Checking using PowerCLI the Datacenters in the vCenter:

~~~ posh
PS C:\Windows\system32> Get-Datacenter

Name
----
vGemba
~~~

Looks like vSphere DSC did what we wanted.

## Cluster Setup

Next up is is a DRS cluster using the resource [DrsCluster](https://github.com/vmware/dscr-for-vmware/blob/master/Documentation/DSCResources/DrsCluster/DrsCluster.md). In this example we create the cluster, enable DRS, set the automation level, and set the Migration Threshold to 3:

~~~ posh
$server = 'vcsa1.corp.contoso.com'

$configurationData = @{
    AllNodes = @(
        @{
            NodeName = 'localhost'
            PSDscAllowPlainTextPassword = $true
            PSDscAllowDomainUser = $true #needed if connecting to vCenter
        }
    )
}

Configuration ClusterSetup {
    Import-DscResource -ModuleName VMware.vSphereDSC

    $Credential = Import-Clixml C:\Credential\a-cwestwater.cred

    Node localhost {
        DrsCluster vCenterCluster {
            Server = $server
            Credential = $credential
            Ensure = 'Present'
            Location = [string]::Empty
            DatacenterLocation = [string]::Empty
            DatacenterName = 'vGemba'
            Name = 'vGemba Cluster'
            DRSEnabled = $true
            DRSAutomationLevel = 'FullyAutomated'
            DrsMigrationThreshold = 3
            }
    }
}

ClusterSetup -ConfigurationData $configurationData
~~~

I don't think I need to explain much about what is happening here. I think that is one of the key things about DSC. Once you get the hang of a configuration is very readable and easy to understand what it's doing. The equivalent in PowerCLI would be:

~~~ posh
New-Cluster -Name "vGemba Cluster" -DrsAutomationLevel FullyAutomated -DrsEnabled -Location vGemba -Server vcsa1.corp.contoso.com
~~~

> You will notice in the code above I don't set the Automation Level - it's not available in New-Cluster

Some may argue you could make it more readable by using [splatting]({{ site.baseurl }}{% post_url 2018-02-04-PowerShell-Splatting %}) but we see another key feature of DSC/Infrastructure as Code. Say for example we scripted out the cluster configuration but then later on someone wanted to change the DRS Automation Level to `Partially Automated`. They may do this in the GUI or a simple PowerCLI command, but would someone go back to the original script that had `New-Cluster` in it and change the value to reflect this? I think we all know the answer to that one...

However if using vSphere DSC you would simply change the entry for `DRSAutomationLevel` to be `DRSAutomationLevel = 'PartiallyAutomated` and run a `Start-DSCConfiguration`and the change is made. You would obviously store the code in version control and then everyone could see how the cluster should be configured and why any changes were made using the Commit history. No more cowboy changes!

Another scenario. Auditors are in and want to see how your environment is configured. You can simply show them the DSC code and if they want proof it's actually running that way, just `Test-DscConfiguration`.

## Chaining Them Together

So if we wanted to configure the cluster and datacenter in the same DSC script, this is easily done:

~~~ posh
$server = 'vcsa1.corp.contoso.com'

$configurationData = @{
    AllNodes = @(
        @{
            NodeName = 'localhost'
            PSDscAllowPlainTextPassword = $true
            PSDscAllowDomainUser = $true #needed if connecting to vCenter
        }
    )
}

Configuration vCSASetup {
    Import-DscResource -ModuleName VMware.vSphereDSC

    $Credential = Import-Clixml C:\Credential\a-cwestwater.cred

    Node localhost {
        DrsCluster vCenterCluster {
            DependsOn = '[Datacenter]vCenterDatacenter'
            Server = $server
            Credential = $credential
            Ensure = 'Present'
            Location = ''
            DatacenterLocation = ''
            DatacenterName = 'vGemba'
            Name = 'vGemba Cluster'
            DRSEnabled = $true
            DRSAutomationLevel = 'PartiallyAutomated'
            DrsMigrationThreshold = '5'
        }

        Datacenter vCenterDatacenter {
            Server = $Server
            Credential = $Credential
            Name = 'vGemba'
            Location = ''
            Ensure = 'Present'
        }
    }
}

vCSASetup -ConfigurationData $configurationData
~~~

Notice a couple of things. In the script order we create the cluster **before** we create the datacenter it should be nested under. This would normally cause a failure in the configuration:

~~~ posh
PS C:\Scripts\vSphereDSC> Test-DscConfiguration -Path .\vCSASetup -Verbose
VERBOSE: Perform operation 'Invoke CimMethod' with following parameters, ''methodName' = TestConfiguration,'className' = MSFT_DSCLocalConfigurationManager,'namespaceName' = root/Microsoft/Win
dows/DesiredStateConfiguration'.
VERBOSE: An LCM method call arrived from computer IT1 with user sid S-1-5-21-2027855393-2688064357-1577006696-1107.
VERBOSE: [IT1]: LCM:  [ Start  Compare  ]
VERBOSE: [IT1]: LCM:  [ Start  Resource ]  [[DrsCluster]vCenterCluster]
VERBOSE: [IT1]: LCM:  [ Start  Test     ]  [[DrsCluster]vCenterCluster]
VERBOSE: [IT1]:                            [[DrsCluster]vCenterCluster] 02/10/2019 18:40:29	Get-View	Finished execution
VERBOSE: [IT1]:                            [[DrsCluster]vCenterCluster] 02/10/2019 18:40:29	Get-Inventory	Finished execution
VERBOSE: [IT1]:                            [[DrsCluster]vCenterCluster] 02/10/2019 18:40:29	Get-Datacenter	Finished execution
VERBOSE: [IT1]: LCM:  [ End    Test     ]  [[DrsCluster]vCenterCluster] False in 4.6720 seconds.
VERBOSE: [IT1]: LCM:  [ *FAILED*Compare  ]     Completed processing compare operation. The operation returned False.
PowerShell DSC resource VMware.vSphereDSC  failed to execute Test functionality with error message: Datacenter vGemba was not found at Datacenters.
    + CategoryInfo          : InvalidOperation: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : ProviderOperationExecutionFailure
    + PSComputerName        : localhost

VERBOSE: Operation 'Invoke CimMethod' complete.
VERBOSE: Time taken for configuration job to complete is 5.149 seconds
~~~

The way to make sure the Datacenter is created before the cluster no matter the configuration running order look in the Resource `vCenterCluster` the first line:

~~~ posh
DependsOn = '[Datacenter]vCenterDatacenter'
~~~

The `DependsOn` parameter means the cluster configuration has to wait until the datacenter is configured first. Remember this tip if you have things that need setup in a particular order.

## Wrap Up

Simple examples again but you don't need to start complicated to get the hang of vSphere DSC. There was also a couple of general DSC tips in there that don't just apply to vSphere DSC.

Part 4 coming soon with some things I discovered about vSphere DSC and where you can learn more about it.
