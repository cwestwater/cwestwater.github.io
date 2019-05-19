---
layout: single
title: "Saving PowerCLI Credentials"
date: 2019-05-19
category: [VMware,PowerShell]
excerpt: "How to save credentials for reuse when connecting to vCenter using PowerCLI"
---
# Introduction

I have to connect to multiple vCenters using PowerCLI with different credentials and it can be a pain to have to keep entering them. Using the PowerShell cmdlets [Export-Clixml](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/export-clixml?view=powershell-5.1) and [Import-Clixml](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/import-clixml?view=powershell-5.1) you can save credentials to a file and easily import them when connecting to vCenter.

This has the benefits of not having to enter the creds and not having to save credentials in any script. Note we can save any type of credential - standalone and Active Directory accounts.

## Saving credentials

The first thing we have to do it get the credentials into a variable to work with:

~~~ posh
$credential = Get-Credential
~~~

Complete the usual credential prompt. Next we need to export the credentials to an encrypted file using `Export-Clixml`.

The parameter required is `-Path` which specifies where to save the file (in this case `C:\Credentials\a-cwestwater.cred`). We pipe it the saved `$credential`:

~~~ posh
$credential | Export-Clixml -Path C:\Credential\a-cwestwater.cred
~~~

This creates the credential file with the password stored securely in it. You can open the `.cred` file to see the contents:

~~~ xml
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">corp\a-cwestwater</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb01000000550a3740b954fd45a69c9b20aee638bc0000000002000000000003660000c00000001000000057524bf9b065d65210922b1fcd9ed8ee0000000004800000a0000000100000001c2043f5855f850caac3ca459bd53cff18000000ca72b91e6efb46921a48014415a9dda1a4a53f9313f01872140000009c48ece385f5278bcedd68055a80dd2b7e47582e</SS>
    </Props>
  </Obj>
</Objs>
~~~

This file is encrypted using the Windows Data Protection API and can only be used with the account it was created under and the computer it was created on. This means you can't share the credential file on a network share or if it's a common account send to someone else for use.

When the password expires simply delete the `.cred` file and save a new copy.

## Using the credentials

Now that the credential file is created we can use it to connect to vCenter using PowerCLI. First we need to import the `cred` file into a variable for use:

~~~ posh
$credential = Import-Clixml -Path C:\Credential\a-cwestwater.cred
~~~

Now we have the credentials saved into the variable we can connect to vCenter:

~~~ posh
Connect-VIServer -Server vcsa.corp.contoso.com -Credential $credential
~~~

You are then connected to the vCenter. For saved PowerCLI scripts just add the first line and then pass the credentials as required.

## Wrap Up

A very simple blog post but one that can save you time and improve security on your scripts.