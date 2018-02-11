---
layout: single
title: "Disabled SMB1 and VCSA Domain Join Failure"
date: 2017-09-01
category: [VMware]
excerpt: "STOP using SMB1 - but this breaks joining a vCenter Server Appliance to a domain. Fix it here"
---
As [Ned Pyle[(https://twitter.com/nerdpyle)] from Microsoft so eloquently put it: "Stop using SMB1. Stop using SMB1. **STOP USING SMB1!**" If Wannacry wasn't a wake up call to remove SMB1 from your network you need to re-evaluate that opinion.

Anyway, I was rebuilding my lab environment at home and disabled SMB1 on all Windows servers (I played it safe and removed the feature and disabled using a GPO). I didn't think this would be a problem until I was trying to join my vCenter Server Appliance to the domain. I just couldn't get it to join.

I tried through the interface which never really gave me a reason so I went to the command line. At the vCSA shell I was getting:

~~~ bash
vcsa:~ \# /opt/likewise/bin/domainjoin-cli join domain.com Domain_Administrator Password
Joining to AD Domain:   corp.contoso.com
With Computer DNS Name: vcsa.corp.contoso.com

Error: ERROR_GEN_FAILURE [code 0x0000001f]
~~~

Searching that error led me to Ned's [SMB1 Product Clearinghouse page](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/). It's a list of all products that require SMB1, and the vCSA is on there. The two links for the vCSA are:
* [VMware KB 2134063](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2134063&sliceId=1&docTypeID=DT_KB_1_1&dialogID=479220377&stateId=0)
* [Enabling vCenter Server Appliance (VCSA) to use SMB2](https://virtualizationnation.com/2017/04/17/enabling-vcenter-server-appliance-vcsa-to-use-smb2/)

Basically vCSA defaults to SMB1 for joining to the domain. SMB2 is supported, just not the default. To fix this at the Shell on the vCSA:

1. Enable the SMB2Enabled Flag in likewise's config:
~~~ bash
/opt/likewise/bin/lwregshell set_value '[HKEY_THIS_MACHINE\Services\lwio\Parameters\Drivers\rdr]' Smb2Enabled 1
~~~
2. Verify:
~~~ bash
/opt/likewise/bin/lwregshell list_values '[HKEY_THIS_MACHINE\Services\l wio\Parameters\Drivers\rdr]'
"Smb2Enabled"      REG_DWORD       0x00000001 (1)
~~~
3. Restart likewise:
~~~ bash
/opt/likewise/bin/lwsm restart lwio
~~~

You will now be able to join the vCSA to the domain.

Hey VMware - how about updating the KB article saying the fix is to enable SMB1 which is proven to be incorrect? Thanks to [Virtualization Nation](https://virtualizationnation.com/) for the real fix.

# Update 13th November 2017

This is an update to the original post contents above. As of vCenter Server 6.0 Update 3c SMB2 is supported without the modifications above. See the [release notes here](https://docs.vmware.com/en/VMware-vSphere/6.0/rn/vsphere-vcenter-server-60u3c-release-notes.html).