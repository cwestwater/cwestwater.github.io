---
layout: single
title: "PowerShell Splatting"
date: 2018-02-04
category: [PowerShell]
excerpt: "PowerShell Splatting can really help with the readability of your code"
---
# Introduction

At the recent [Scottish PowerShell and Devops User Group](https://psdevopsug.scot/) meeting during an excellent talk from [Rob Swell](https://twitter.com/@sqldbawithbeard) he mentioned [PowerShell Splatting](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_splatting?view=powershell-5.1). I immediately thought it was a very nice way of dealing with long PowerShell commands and makes them easier to read. This will help a great deal when others are reading your code.

## Hash Tables

The key to splatting is to create a hash table with a series of key value pairs. We build a hash table with the parameters and the values needed for a cmdlet. A good example is the PowerCLI command [New-VM](https://code.vmware.com/doc/preview?id=5975#/doc/New-VM.html). Looking at the documentation it can have many potential parameters:

~~~ posh
New-VM [-AdvancedOption <AdvancedOption[]>] [[-VMHost] <VMHost>] [-Version <VMVersion>] -Name <String> [-ResourcePool <VIContainer>] [-VApp <VApp>] [-Location <Folder>] [-Datastore <StorageResource>] [-DiskMB <Int64[]>] [-DiskGB <Decimal[]>] [-DiskPath <String[]>] [-DiskStorageFormat <VirtualDiskStorageFormat>] [-MemoryMB <Int64>] [-MemoryGB <Decimal>] [-NumCpu <Int32>] [-CoresPerSocket <Int32>] [-Floppy] [-CD] [-GuestId <String>] [-AlternateGuestName <String>] [-NetworkName <String[]>] [-Portgroup <VirtualPortGroupBase[]>] [-HARestartPriority <HARestartPriority>] [-HAIsolationResponse <HAIsolationResponse>] [-DrsAutomationLevel <DrsAutomationLevel>] [-VMSwapfilePolicy <VMSwapfilePolicy>] [-Server <VIServer[]>] [-RunAsync] [-Notes <String>] [-WhatIf] [-Confirm] [<CommonParameters>] 
~~~

When I am creating a new VM for my MDT GOLD image I have in my PowerCLI script the line:

~~~ posh
New-VM -Name MDT-W7-GOLD -CD -Datastore NFS-Datastore -DiskGB 32 -DiskStorageFormat Thin -GuestId windows7_64Guest -MemoryGB 6 -NumCpu 2 -NetworkName VM Network -VMHost esxi1.corp.contoso.com
~~~

That's quite a long command that to someone new to PowerShell/PowerCLI would take some time to figure out. We can improve the readability by converting the parameter and value to a hash table.

The format is quite simple:

~~~ posh
$hashTableName = @{
    parameter1 = "value1"
    parameter2 = "value2"
    parameter3 = "value3"
}
~~~

So in the my `New-VM` example above we can convert everything after `New-VM` to:

~~~ posh
$vmDetails = @{
    Name = 'MDT-W7-GOLD'
    CD = $true
    Datastore = 'NFS-Datastore'
    DiskGB = 32
    DiskStorageFormat = 'Thin'
    GuestId = 'windows7_64Guest'
    MemoryGB = 6
    NumCpu = 2
    NetworkName = 'VM Network'
    VMHost = 'esxi1.corp.contoso.com'
}
~~~

You can see here we are using the same parameters and values as before but they are much easier to read. You will also see the parameter `-CD`. This be default does not have a value in `New-VM` so in the hash table we have to say `CD = $true` to get round this.

You can also use variables defined outside the hash table as values. For example:

~~~
$Datastore = 'NFS-Datastore'

$vmDetails = @{
    Name = 'MDT-W7-GOLD'
    CD = $true
    Datastore = $Datastore
    DiskGB = 32
    DiskStorageFormat = 'Thin'
    GuestId = 'windows7_64Guest'
    MemoryGB = 6
    NumCpu = 2
    NetworkName = 'VM Network'
    VMHost = 'esxi1.corp.contoso.com'
}
~~~

## Using the Hash Table

This is **very** simple. We just use the cmdlet and pass the hash table using `@hashTableName`. In our example:

~~~ posh
New-VM @vmDetails
~~~

That's it! You can also pass other parameters that are not in the hash table. For example:

~~~ posh
$vmDetails = @{
    CD = $true
    Datastore 'NFS-Datastore'
    DiskGB = 32
    DiskStorageFormat = 'Thin'
    GuestId = 'windows7_64Guest'
    MemoryGB = 6
    NumCpu = 2
    NetworkName = 'VM Network'
    VMHost = 'esxi1.corp.contoso.com'
}

New-VM @vmDetails -Name 'MDT-W7-GOLD'
~~~

You see we didn't define the VM Name in the hash table so used it on the cmdlet.

So think that through a bit more. Say we want to spin up a bunch of servers? We can reuse the hash table multiple times:

~~~ posh
$vmDetails = @{
    CD = $true
    Datastore 'NFS-Datastore'
    DiskGB = 32
    DiskStorageFormat = 'Thin'
    GuestId = 'windows7_64Guest'
    MemoryGB = 6
    NumCpu = 2
    NetworkName = 'VM Network'
    VMHost = 'esxi1.corp.contoso.com'
}

New-VM @vmDetails -Name 'DatabaseVM'
New-VM @vmDetails -Name 'ApplicationVM'
New-VM @vmDetails -Name 'WebServerVM'
~~~

# Conclusion

I am a firm believer in making scripts as easy to follow and readable as possible and splatting helps with this. I fully intend to use this technique in the future.