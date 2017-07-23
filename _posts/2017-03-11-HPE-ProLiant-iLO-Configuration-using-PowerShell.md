---
layout: single
title: "HPE ProLiant iLO Configuration using PowerShell"
date: 2017-03-11
category: [Microsoft, PowerShell]
excerpt: "Use PowerShell to configure your HPE iLO"
---
We have pretty much standardised on HPE ProLiant servers.  As part of this is the useful iLO interface for out of band management. In the past we have configured the iLO's manually using the GUI. That has all changed now that HPE have iLO cmdlet's to build and manage the iLO's using PowerShell.

As part of my drive for CI and a personal goal of automating/scripting at least one process a month I saw an opportunity to convert the manual process to a script. This would result in a repeatable, standard process for configuring an iLO.  It would not really provide much time and cost savings as this is really only done 3-4 times a year.

A pre-requisite for this is to install the iLO cmdlet's on your computer.  They can be downloaded here: [HPE Scripting Tools for Windows PowerShell](https://www.hpe.com/us/en/product-catalog/detail/pip.5440657.html)

First of all lets look at the tasks I need to do to setup an iLO:
1. Add a DNS A record on the local Domain Controller for the iLO
2. Add the standard login details we use for authentication (local users, no AD authentication)
3. Remove the default login
4. Patch up the the latest firmware revision
5. Add the iLO Advanced license key
6. Configure iLO Network and hostname
7. Configure SNTP
8. Configure SNMP
9. Setup AlertMail
10. Change the Power options

This is a total of 40 manual steps taking an average of 15-20 minutes.  Let automate!

The code is pretty readable and commented, so I will let it lead you through. Please note this works for us.  You can easily alter it for your setup.  Also, lines 51 and 55 need changed for your SNMP and email setup.  With this script you can provision an iLO in less than two minutes.

~~~ posh
#Variables
$iLODHCPIP = 'Enter the DHCP IP Address issued to the new iLO'
$DefaultiLOPassword = 'Enter default iLO password.  Found on label on server'
$iLOLicenseKey = 'Enter the iLO Advanced License key'
$cred = Get-Credential -UserName admin -Message "Enter current standard iLO password"
$DNSServer = 'DNS Server'
$iLOIPAddress = 'Enter fixed IP for the iLO'
$iLOGateway = 'Enter the Default Gateway'
$iLOPrimaryDNS = 'Enter Primary DNS IP'
$iLOSecondaryDNS ='Enter Secoondary DNS IP'
$Zone = 'DNS Zone Name'
$iLODNSName = 'Enter iLO Hostname'
$iLOFQDN = 'Enter iLO FQDN'
$TimeZone = 'Europe/London'
$iLOFirmwareFile = 'Path to downloaded firmware'

# Add DNS A Record to wbc.local zone
Write-Host "Adding DNS A Record $iLODNSName with IP $iLOIPAddress on $DNSServer"
Add-DnsServerResourceRecordA -IPv4Address $iLOIPAddress -Name $iLODNSName -ZoneName $Zone -ComputerName $DNSServer

# Disable Certificate Authentication
Disable-HPiLOCertificateAuthentication

# Add the standard admin account with the network password
Write-Host 'Adding standard admin user account'
Add-HPiLOUser -Username Administrator -Password $DefaultiLOPassword -Server $iLODHCPIP -AdminPriv Yes -ConfigILOPriv Yes -NewPassword $cred.Password -NewUserLogin $cred.UserName -NewUsername $cred.UserName  -RemoteConsPriv Yes -ResetServerPriv Yes -VirtualMediaPriv Yes

# Remove the built in default Administrator account
Remove-HPiLOUser -Credential $cred -Server $iLODHCPIP -RemoveUserLogin Administrator

# Update the iLO firmware
Write-Host "Applying the latest iLO Firmware.  This could take a minute"
Update-HPiLOFirmware -Credential $cred  -Server $iLODHCPIP -Location $iLOFirmwareFile
Start-Sleep -s 60

# Add the iLO license key
Write-Host "Adding the iLO Advanced License"
Set-HPiLOLicenseKey -Credential $cred -Server $iLODHCPIP -Key $iLOLicenseKey

# Setup iLO networking
Write-Host "Setting up network on $iLODNSName"
Set-HPiLOServerName -Credential $cred -Server $iLODHCPIP -ServerName $iLODNSName
Set-HPiLOServerFQDN -Credential $cred -Server $iLODHCPIP -ServerFQDN $iloFQDN
Set-HPiLOIPv6NetworkSetting -Credential $cred -Server $iLODHCPIP -DHCPv6SNTPSetting No
Set-HPiLONetworkSetting -Credential $cred -Server $iLODHCPIP -DHCPEnable No -DHCPSNTP No -DNSName $iLODNSName -Domain $Zone -IPAddress $iLOIPAddress -GatewayIP $iLOGateway -PingGateway Yes -PrimDNSServer $iLOPrimaryDNS -RegDDNSServer Yes -RegWINSServer No -SecDNSServer $iloSecondaryDNS -SNTPServer1 $iLOPrimaryDNS -SNTPServer2 $iloSecondaryDNS -SubnetMask 255.255.248.0 -Timezone $TimeZone
Start-Sleep -s 30
Write-Host 'Pausing for 30 seconds for iLO reset with new IP'

# Setup SNMP
Write-Host "Configuring SNMP"
Set-HPiLOSNMPIMSetting -Credential $cred -Server $iLODNSName -SNMPAddress1 'SNMP Server' -SNMPAddress1ROCommunity public -SNMPAddress1TrapCommunityValue provate -SNMPAddress2 localhost -SNMPSysContact "Ops Team" -SNMPSysLocation GLA -SNMPSystemRole "VMware ESXi Host" -WebAgentIPAddress $iLOFQDN

# Setup Alertmail
Write-Host "Configuring email alerts"
Set-HPiLOGlobalSetting -Credential $cred -Server $iLODNSName -AlertMail Yes -AlertMailEmail 'Email address' -AlertMailSenderDomain 'email domain' -AlertMailSMTPPort 25 -AlertMailSMTPServer 'SMTP Relay'

# Setup Power options
Write-Host "Configuring power options"
Set-HPiLOHostAPO -Credential $cred -Server $iLODNSName -ServerAutoPower On
Set-HPiLOHostAPO -Credential $cred -Server $iLODNSName -ServerAutoPower Random

# Restart iLO
Write-Host "Configuration complete.  Restarting iLO $iLODNSName"
Reset-HPiLORIB -Credential $cred -Server $iLODNSName
~~~