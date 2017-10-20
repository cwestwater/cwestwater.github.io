---
layout: single
title: "Windows 10 Fall Creators Update and Synology NAS"
date: 2017-10-20
category: [Microsoft]
excerpt: "Windows 10 Fall Creators Update, Synology NAS and SMBv1 is not a good combination"
---
Windows 10 Fall Creators Update (version 1709) was released this week and I updated my main PC at home. Very quickly I noticed trying to open any file on my Synology, no matter what type, was failing with the error:

![Synology Error]({{ site.url }}/assets/images/Synology-SMB1-error.png)

Copying the file to a local disk had no issues. I knew it must be something to do with the PC as my work laptop using the previous Windows 10 version had no issues.

Then I remembered reading Microsoft announced that the [Fall Creators Update would disable SMB1](https://support.microsoft.com/en-us/help/4034314/smbv1-is-not-installed-windows-10-and-windows-server-version-1709) **by default**. This is a great decision by Microsoft in my opinion, but it was causing me an issue. Note too in the Microsoft article it does not list the error I was getting.

You can check if SMB1 is enabled or not using PowerShell:

~~~ posh
PS C:\WINDOWS\system32> Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol | select State

   State
   -----
Disabled
~~~

You can see SMB1 is disabled.

So there are two choices - enable SMB1 or get the Synology to use a newer version of SMB.

# Enable SMB1 in Windows 10

This is not recommended, but it will fix this particular issue. To enable SMB1 you can use the Control Panel and enable the SMB1 Feature:

![Enable SMB1]({{ site.url }}/assets/images/Enable-SMB1.png)

but PowerShell is the way I try to do most things:

~~~ posh
PS C:\WINDOWS\system32> Enable-WindowsOptionalFeature -FeatureName SMB1Protocol -Online


Path          : 
Online        : True
RestartNeeded : True

PS C:\WINDOWS\system32> Restart-Computer
~~~

You can now access the files on the Synology.

# 'Disable' SMB1 on Synology

A much better solution is to disable SMB1 on the Synology. Technically you can't just select a check box to disable it. You change an advanced setting in SMB to say the Minimum SMB protocol is SMB2. This means SMB1 is not used on the Synology.

To change this setting in DSM browse to ```Control Panel...File Services...SMB/AFP/NFS``` and click on ```Advanced```:

![Synology SMB2]({{ site.url }}/assets/images/Synology-SMB2-01.png)

Then change ```Maximum SMB Protocol:``` to ```SMB3``` and ```Minimum SMB Protocol:``` to ```SMB2```:

![Synology SMB2]({{ site.url }}/assets/images/Synology-SMB2-02.png)

While you change the options you will see in ```SMB Range:``` the supported SMB protocols change.

Apply the changes and you should be now using SMB2 at least with your Windows 10 machine, but it should automatically use SMB3.

You can check the SMB version being used through PowerShell:

~~~ posh
PS C:\WINDOWS\system32> Get-SmbConnection

ServerName ShareName UserName                   Credential                 Dialect NumOpens
---------- --------- --------                   ----------                 ------- --------
NAS-01     home      WIN10\cwestwater           WIN10\cwestwater           3.1.1   1
~~~

Note the Dialect is 3.1.1 - SMB3.

If you had chosen SMB2 as both the Maximum and Minimum SMB Protocol on the Synology you would see:

~~~ posh
PS C:\WINDOWS\system32> Get-SmbConnection

ServerName ShareName UserName                   Credential                 Dialect NumOpens
---------- --------- --------                   ----------                 ------- --------
NAS-01     home      WIN10\cwestwater           WIN10\cwestwater           2.0.2   1
~~~

# Wrap up

Synology devices are very popular and as Windows 10 Fall Creators Update starts hitting machines I hope someone finds this useful. I **highly recommend** you change the SMB version on the Synology and **do not enable** SMB1 on Windows 10.