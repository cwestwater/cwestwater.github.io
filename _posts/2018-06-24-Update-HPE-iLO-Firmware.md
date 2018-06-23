---
layout: single
title: "Updating HPE iLO Firmware using PowerShell"
date: 2018-06-24
category: [Microsoft,PowerShell]
excerpt: "How to update the firmware on multiple iLOs using the HPE PowerShell cmdlets"
---

# Introduction

On the [Scottish VMUG](http://www.scottishvmug.com/) Slack the other day [James Cruickshank](https://twitter.com/vCrooky) alerted us about a [vulnerability](https://www.cvedetails.com/cve/CVE-2017-12542/) on HPE iLO 4's running firmware prior to v2.53.

I was already covered as I was running 2.55 but I thought I would detail how I use the HPE iLO PowerShell cmdlets to mass update my iLOs.

## v2.00 of the HPE iLO Cmdlets

I previously wrote about HP iLO Cmdlets in the post [HPE ProLiant iLO Configuration using PowerShell]({{ site.baseurl }}{% post_url 2017-03-11-HPE-ProLiant-iLO-Configuration-using-PowerShell %}) back in February 2017. Back then the Cmdlets were snap-ins that needed to be installed via a downloaded installer.

I'm glad to say HPE have released them now as a module in the PowerShell Gallery. If you have the previous version installed uninstall the program. You can then go to a PowerShell session (ran as an Administrator) and install from the gallery:

~~~ posh
Install-Module -Name HPEiLOCmdlets

Get-InstalledModule -Name HPEiLOCmdlets

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
2.0.0.0              HPEiLOCmdlets                       PSGallery            Scripting Tools for Windows PowerShell : iLO Cmdlets...
~~~

## Defining the variables

Lets setup some required variables first:

~~~ posh
# Variables
$iLOs = "10.10.1.100-120"
$iLOType = "Integrated Lights-Out 4 (iLO 4)"
$firmwareVersion = "2.60"
$firmwareFile = "C:\Temp\ilo4_260.bin"

$username = "Administrator"
$password = ConvertTo-SecureString -String "Password1" -AsPlainText -Force
$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $username,$password
~~~

Let's look at what we did. First of all we set an IP range we scan for iLOs to respond on. I keep my iLOs on a defined range of addresses such as `10.10.1.100-120`. Next we are only interested in iLO 4's so we define the type in `$iLOType`. Valid entries are:

* Integrated Lights-Out 3 (iLO 3)
* Integrated Lights-Out 4 (iLO 4)
* Integrated Lights-Out 5 (iLO 5)

Next we define the iLO version we want to upgrade to (2.60 is the latest available) and where we are storing the downloaded firmware file.

Finally we store some credentials. Username is a plain text but we need to store the password as a secure string. Next we combine the username and password to create a credential object. This will be used later to connect to the iLOs.

## Find the iLOs to Update

Next up is to find the servers we need to update and put them into a variable:

~~~ posh
# Find all iLO's that don't match the required version
$foundServers = Find-HPEiLO -Range $iLOs | Where-Object -Property PN -EQ $iLOType | Where-Object -Property FWRI -NE $FirmwareVersion
~~~

I'm going to break that down. First of all we are going to put the list of iLOs to update into a variable called `$foundServers`. We use the cmdlet `Find-HPEiLO` to do this. We pass it the `-Range` of IP's we want to scan, which we defined as the variable `$iLOs`. From all the servers that respond in the list we only want to select the ones requiring a firmware update.

First we only find iLO 4's so we use `Where-Object -Property PN -EQ $iLOType`. We are saying to only choose iLOs where the iLO Property Part Number, `PN` is equal `-EQ` to the variable `$iLOType`. Next we refine this list even further. We check the firmware version property `FWRI` and find where that is not equal `NE` to the firmware version defined in the variable `$firmwareVersion`.

If you want to check which servers it found, just type in `$foundServers` and it will list all the servers identified.

## Connecting to the iLOs

Now we have a list of the servers in the variable `$foundServers` we need to connect to them. We use the cmdlet `Connect-HPEiLO`:

~~~ posh
# Connect the iLOs that need updated
$connection = $foundServers | Connect-HPEiLO -Credential $credential
~~~

We are creating another variable `$connection` and passing to it the connected iLOs. We use pipe the list of iLOs, `$foundServers` to `Connect-HPEiLO`. We send the credential object we created earlier for authentication. Note if you don't have the iLOs signed by a valid certificate you will need to add the parameter `-DisableCertificateAuthentication`.

So we now have a connection setup to all the iLOs that need an update.

## Updating the Firmware

This is very simple:

~~~ posh
Update-HPEiLOFirmware -Connection $connection -Location $firmwareFile -Confirm:$false
~~~

We pass the `$connection` created with the Location of the firmware file on the local drive. If you want to skip the confirmation use `-Confirm:$false`. The firmware is pushed to the iLOs.

## Verification of the Update

So we sound check the firmware was applied. We use the cmdlet `Get-HPEiLOFirmwareVersion` passing the `$connection` again:

~~~ posh
Get-HPEiLOFirmwareVersion -Connection $connection
~~~

## Disconnecting from the iLOs

Finally we close the connection to the iLOs:

~~~ posh
Disconnect-HPEiLO -Connection $connection
~~~

## Sample Run

Here is an output of a sample run of the the cmdlets:

~~~ posh
PS C:\> $iLOs = "10.10.1.100-120"
$iLOType = "Integrated Lights-Out 4 (iLO 4)"
$firmwareVersion = "2.60"
$firmwareFile = "C:\Temp\ilo4_260.bin"
$username = "Administrator"
$password = ConvertTo-SecureString -String "Password1" -AsPlainText -Force
$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $username,$password

PS C:\> $foundServers = Find-HPEiLO $iLOs | Where-Object -Property PN -EQ $iLOType | Where-Object -Property FWRI -NE $FirmwareVersion
WARNING: It might take a while to search for all the iLOs if the input is a very large range.Use Verbose for more information
.

PS C:\> $foundServers

IP           : 10.10.1.100
Hostname     : esxi-01-ilo.corp.contoso.com
SPN          : ProLiant DL360p Gen8
FWRI         : 2.55
PN           : Integrated Lights-Out 4 (iLO 4)
SerialNumber : XXXXXXXXXX
cUUID        : 39363436-3130-5A43-1234-333730433351

IP           : 10.10.1.101
Hostname     : esxi-02-ilo.corp.contoso.com
SPN          : ProLiant DL360 Gen9
FWRI         : 2.55
PN           : Integrated Lights-Out 4 (iLO 4)
SerialNumber : XXXXXXXXXX
cUUID        : 32353537-3236-584D-1234-323030364C44

IP           : 10.10.1.107
Hostname     : ma-01-ilo.corp.contoso.com
SPN          : ProLiant DL380p Gen8
FWRI         : 2.55
PN           : Integrated Lights-Out 4 (iLO 4)
SerialNumber : XXXXXXXXXX
cUUID        : 35343037-3935-5A43-1234-3432324B3138

PS C:\> $Connection = $foundServers | Connect-HPEiLO -Credential $credential

PS C:\> Update-HPEiLOFirmware -Connection $connection -Location $firmwareFile -Confirm:$false
WARNING: Update is in progress, this might take several minutes.

IP            Hostname                     Status StatusInfo
--            --------                     ------ ----------
10.10.1.100   esxi-01-ilo.corp.contoso.com WARNING HPE.iLO.Response.StatusInfo
10.10.1.101   esxi-02-ilo.corp.contoso.com WARNING HPE.iLO.Response.StatusInfo
10.10.1.107   ma-01-ilo.corp.contoso.com   WARNING HPE.iLO.Response.StatusInfo

PS C:\> Get-HPEiLOFirmwareVersion -Connection $connection

FirmwareDate    : May 23 2018
FirmwareVersion : 2.60
ManagerType     : iLO 4
IP              : 10.10.1.100
Hostname        : esxi-01-ilo.corp.contoso.com
Status          : OK
StatusInfo      :

FirmwareDate    : May 23 2018
FirmwareVersion : 2.60
ManagerType     : iLO 4
IP              : 10.10.1.101
Hostname        : esxi-02-ilo.corp.contoso.com
Status          : OK
StatusInfo      :

FirmwareDate    : May 23 2018
FirmwareVersion : 2.60
ManagerType     : iLO 4
IP              : 10.10.1.107
Hostname        : ma-01-ilo.corp.contoso.com
Status          : OK
StatusInfo      :

PS C:\> Disconnect-HPEiLO -Connection $Connection
~~~

## Conclusion

As you can see with a few simple commands you can automate the installation across many iLOs at the same time. Why waste time manually logging into each iLO web console and pushing the firmware. Automate all the things.

There are lots of other iLO cmdlets available as part of the module:

~~~ posh
PS C:> Get-Command -Name *HPE* | measure

Count    : 214
~~~

Go investigate!