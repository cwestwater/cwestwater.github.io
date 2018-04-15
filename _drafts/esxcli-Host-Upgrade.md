---
layout: single
title: "ESXi CLI Upgrade"
# date: 2018-04-08
category: [VMware]
excerpt: "How to upgrade a host using esxcli"
---
# Introduction

I had to upgrade a host from ESXi 5.0 (yikes!) to 6.0 recently. The server was in Texas, I was in Scotland, it was standalone so no Update Manager, and there was no iDRAC on the server. In this case I decided to use esxcli to do the upgrade.

## ESXi Offline Bundle

Usually I would use the iso file download, mount it via iLO or iDRAC, and perform the upgrade by booting to it and running through the prompts. In this case I had to download the offline bundle zip file:

![ESXi Update]({{ site.url }}/assets/images/ESXiUpdate-01.png)

The ESXi Offline Bundle needs to be uploaded to a datastore that the host can access. This can be done using the vSphere client or using SCP to upload the zip file.

## Performing the Upgrade

The process for performing the upgrade is pretty simple:

* Make sure all VM's are powered off and the host is in maintenance mode
* Enable SSH on the host and use something like PuTTY or SecureCRT to connect to the host
* Verify the profile you want to install from the bundle using `esxcli software sources profile list`:

~~~ bash
~ # esxcli software sources profile list --depot /vmfs/volumes/datastore/ESXi600-201706001.zip
Name                              Vendor        Acceptance Level
--------------------------------  ------------  ----------------
ESXi-6.0.0-20170604001-no-tools   VMware, Inc.  PartnerSupported
ESXi-6.0.0-20170601001s-no-tools  VMware, Inc.  PartnerSupported
ESXi-6.0.0-20170604001-standard   VMware, Inc.  PartnerSupported
ESXi-6.0.0-20170601001s-standard  VMware, Inc.  PartnerSupported
~~~

* In this case I want to use profile `ESXi-6.0.0-20170604001-standard`. To apply the update (I have cropped out the full list of applied VIBs):

~~~ bash
~ # esxcli software profile update --depot /vmfs/volumes/datastore/ESXi600-201706001.zip --profile ESXi-6.0.0-20170604001-standard
Update Result
   Message: The update completed successfully, but the system needs to be rebooted for the changes to be effective.
   Reboot Required: true
   VIBs Installed: VMWARE_bootbank_mtip32xx-native_3.8.5-1vmw.600.0.0.2494585, VMware_bootbank_ata-pata-amd_0.3.10-3vmw.600.0.0.2494585, VMware_bootbank_ata-pata-atiixp_0.4.6-4vmw.600.0.0.2494585, VMware_bootbank_ata-pata-cmd64x_0.2.5-3vmw.600.0.0.2494585,
~~~

* You can see the update completed successfully. Reboot the host:

~~~ bash
~ # reboot
~~~

Once the host is rebooted you can see it's running v6.0:

![ESXi Update]({{ site.url }}/assets/images/ESXiUpdate-02.png)

## Profiles

You could see above when I ran `esxcli software sources profile list` the zip file had four profiles. Which one do you choose?

| Name | Description |
| --- | --- |
| ESXi-6.0.0-20170604001-no-tools | All patches but no VMware Tools included |
| ESXi-6.0.0-20170604001s-no-tools | Security patches only but no VMware Tools included |
| ESXi-6.0.0-20170604001-standard | All patches and VMware Tools included |
| ESXi-6.0.0-20170604001s-no-tools | Security patches only and VMware Tools included |

I wanted to include all patches and VMware Tools so I chose `ESXi-6.0.0-20170604001-standard`.

## Conclusion

The same principle is available for any ESXi upgrades. This is a good option for upgrades instead of manually running through the installer.