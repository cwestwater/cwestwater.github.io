---
layout: single
title: "MDT Gold Image VM Automation"
date: 2017-02-19
category: [VMware, Microsoft, MDT, PowerCLI]
excerpt: "Use PowerCLI to build your MDT GOLD Image"
---
As part of my role I maintain and develop our Microsoft Deployment Toolkit (MDT) infrastructure. It's a fantastic tool for automating the deployment of your corporate images.

One vital task is to maintain the Gold images with the latest Windows updates.  I tend to do this monthly to speed up deployments.  As part of this task I was manually creating a new VM, customising it then launching the deployment.  Non value add work!

So I turned to PowerCLI to automate the creation of the VM's for our Windows 7 and 10 Gold images.  My goal was to also make this as simple as possible so I could hand off to our Service Desk Tier II team.

As part of that I wanted to make the script have a simple menu where the person running the script could select either Windows 7 or Windows 10. This is actually quite simple using the System.Management.Automation.Host.ChoiceDescription class in PowerShell.  There is a good TechNet article [here](https://technet.microsoft.com/en-us/library/ff730939.aspx) on the procedure.

So the flow of the script is as follows.  

First we need to declare our variables: the vCenter, the path to the WinPE ISO file for booting the VM into MDT, the datastore we put the VM onto, the Network and the actual host we want the VM on.

Next we connect to vCenter, pretty straightforward.  I like giving some feedback on the console so it says if it connects or not.

Next we create the menu. We create some variables for the menu such as the title, a description and then the three options we want to present.  These are Windows 7 GOLD Image, Windows 10 GOLD Image, and Exit. The last line of the menu portion builds the menu on screen.  Depending on the option chose the menu returns a value of 0, 1 or 2.  The script then jumps to the portion that matches this value using and If statement.

The building of the VM for Windows 7 or 10 is pretty similar.  All that is different is the VM name and the GuestID.  First of all we check to see if there is already a VM of the same name in vCenter, Maybe we forgot to delete it after we captured the image.  I don't bother confirming the deletion of the VM.  Next we create the VM with a CD drive, on the datastore specified in the variable, 32GB HDD, Thin provisioned, the correct GuestID, 6GB RAM, 2 CPU's, on the Production network and on the correct host.

If you want to specify a VMXNET3 nic you can comment out the next line.  Next we have to remove the existing CD drive, then add one back in pointing to the WinPE ISO file.

Finally we power on the VM. It boots to the WinPE ISO file which then connects the VM to the MDT Deployment share.  I like cleaning up after myself so we disconnect from vCenter.

The script is pretty simple, but I estimate is saves me 10-15 minutes each time I want to create an image.  Multiply that over the number of images created in a year and it's a pretty good time saving.

Here is the script:

~~~ posh
#Variables  
$vCenterHost="XXXX"  
$Win7VM = "MDT-W7-GOLD"  
$Win10VM = "MDT-W10-GOLD"  
$ISOPath = "XXXX"  
$vmDatastore = "XXXX"  
$vmNetwork = "XXXX"  
$vmHost = "XXXX"  
  
# Connect to vCenter  
Write-Host -NoNewline " Connecting to vCenter..."  
Connect-VIServer $vCenterHost -ErrorAction SilentlyContinue -WarningAction SilentlyContinue | out-null  
if(!$?){  
     Write-Host -ForegroundColor Red " Could not connect to $vCenterHost"  
     exit 2  
}  
else{  
     Write-Host "Connected"  
}  
  
#Create Menu  
$resourceTitle = "MDT GOLD Image Creation"  
$resourceDesc = "Select either Windows 7 or Windows 10"  
$win7 = New-Object System.Management.Automation.Host.ChoiceDescription "Win &7", "Windows 7 GOLD Image"  
$win10 = New-Object System.Management.Automation.Host.ChoiceDescription "Win &10", "Windows 10 GOLD Image"  
$exit = New-Object System.Management.Automation.Host.ChoiceDescription "E&xit", "Exit to PS Prompt."  
$options = [System.Management.Automation.Host.ChoiceDescription[]]($win7, $win10, $exit)  
$resourceResult = $host.ui.PromptForChoice($resourceTitle, $resourceDesc, $options, 2)  
  
# Build Windows 7 VM  
If ($resourceResult -eq 0) {  
$W7Exist = Get-VM -Name $Win7VM -ErrorAction SilentlyContinue  
if ($W7Exist -ne $null)  
{  
Write-Host "Removing existing Windows 7 VM"  
Remove-VM -deletefromdisk -VM $Win7VM -confirm:$false  
}  
  
Write-Host "Creating new Windows 7 VM"  
New-VM -Name $Win7VM -CD -Datastore $vmDatastore -DiskGB 32 -DiskStorageFormat Thin -GuestId windows7_64Guest -MemoryGB 6 -NumCpu 2 -NetworkName $vmNetwork -VMHost $vmHost | Out-Null
#Get-VM $Win7VM | Get-NetworkAdapter | Set-NetworkAdapter -Type vmxnet3 -Confirm:$false | Out-Null
$remcd = Get-CDDrive -VM $Win7VM  
Remove-CDDrive -CD $remcd -Confirm:$false  
New-CDDrive -VM $Win7VM -IsoPath $ISOPath -StartConnected -WarningAction SilentlyContinue | Out-Null  
Write-Host "Completed successfully. Created VM" $Win7VM  
  
Write-Host "Powering on VM"  
Start-VM -VM $Win7VM | Out-Null  
  
Disconnect-VIServer -Server $vCenterHost -Confirm:$false  
Write-Host "Disconnected from $vCenterHost"  
  
Exit  
}  
  
#Build Windows 10 VM  
If ($resourceResult -eq 1) {  
$W10Exist = Get-VM -Name $Win10VM -ErrorAction SilentlyContinue  
if ($W10Exist -ne $null)  
{  
Write-Host "Removing existing Windows 10 VM"  
Remove-VM -deletefromdisk -VM $Win10VM -confirm:$false  
}  
  
Write-Host "Creating new Windows 10 VM"  
New-VM -Name $Win10VM -CD -Datastore $vmDatastore -DiskGB 32 -DiskStorageFormat Thin -GuestId windows8_64Guest -MemoryGB 6 -NumCpu 2 -NetworkName $vmNetwork -VMHost $vmHost | Out-Null
#Get-VM $Win10VM | Get-NetworkAdapter | Set-NetworkAdapter -Type vmxnet3 -Confirm:$false | Out-Null
$remcd = Get-CDDrive -VM $Win10VM  
Remove-CDDrive -CD $remcd -Confirm:$false  
New-CDDrive -VM $Win10VM -IsoPath $ISOPath -StartConnected -WarningAction SilentlyContinue | Out-Null  
Write-Host "Completed successfully. Created VM" $Win10VM  
  
Write-Host "Powering on VM"  
Start-VM -VM $Win10VM | Out-Null  
    
Disconnect-VIServer -Server $vCenterHost -Confirm:$false  
Write-Host "Disconnected from $vCenterHost"  
  
Exit  
}  
  
# Exit to the PowerShell Prompt  
If ($resourceResult -eq 2) {  
Disconnect-VIServer -Server $vCenterHost -Confirm:$false  
Write-Host "Disconnected from $vCenterHost and exiting script"  
Exit  
}  

~~~
