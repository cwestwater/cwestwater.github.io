---
layout: single
title: "vCenter Server Appliance CLI Install"
date: 2018-03-18
category: [VMware]
excerpt: "How to install the vCSA using the CLI Install and json file"
---
# Introduction

I was setting up a new v6.5 Platform Services Controller (PSC) and vCenter Server Appliance (vCSA) and noticed you could setup in a couple of different ways. The traditional GUI way or by using a customised json file and launching the install from the command line.

Even though building a new vCenter is something I only have to do very occasionally I wanted to use the CLI installer.  There were a number of reasons for this. First I'd never done it via CLI so it was something new to learn. Secondly if any auditor/other team member/compliance asks how the vSphere environment was built I can point them at the json file that holds all the details. Thirdly if I am ever hit by bus and someone needs to recreate it, they can pull the files from a repository and repeat it.

Note, as with any VMware deployment ensure that there are forward and reverse DNS records for all the appliances.

## json Template Files

The key to building the PSC and/or the vCSA is by altering a json template that is suitable for your deployment scenario. The templates are included in the vCSA download. Extract the installer and browse to the `vcsa-cli-installer\templates` folder. In there you can see there are three folders: `install`, `migrate` and `upgrade`. In there are the json files for the various scenarios. I am just going to highlight the install templates:

| Location | Template | Description |
| --- | --- | --- |
| \install\ | embedded_vCSA_on_ESXi.json | Deploy vCSA and embedded PSC to an ESXi host |
| | embedded_vCSA_on_VC.json | Deploy vCSA and embedded PSC to vCenter |
| | PSC_first_instance_on_ESXi.json | Deploy first external PSC to an ESXi host |
| | PSC_first_instance_on_VC.json | Deploy first external PSC to a vCenter |
| | PSC_replication_on_ESXi.json | Deploy a second PSC to an existing SSO Domain to an ESXi host |
| | PSC_replication_on_VC.json | Deploy a second PSC to an existing SSO Domain to a vCenter |
| | vCSA_on_ESXi.json | Deploy a vCSA only to an ESXi host |
| | vCSA_on_VC.json | Deploy a vCSA only to a vCenter |

There are templates for scenarios such as `migrate\winvc5.5\win_vc_to_vCSA_on_ESXi.json` which is for:

> Sample template to migrate a Windows installation of vCenter Server 5.5 with an external vCenter Single Sign-On instance to a vCenter Server Appliance 6.5 with an external Platform Services Controller on an ESXi host.

Pretty must any scenario can be covered using the json templates.

## Customising the json file

In my scenario I needed to alter two of the json files: `\install\PSC_first_instance_on_ESXi.json` and `\install\vCSA_on_ESXi.json`. Obviously we need to deploy the external PSC first. Lets look at the default template:

~~~ json
{
    "__version": "2.3.0",
    "__comments": "Sample template to deploy a Platform Services Controller appliance as the first instance in a new vCenter Single Sign-On domain on an ESXi host.",
    "new.vcsa": {
        "esxi": {
            "hostname": "<FQDN or IP address of the ESXi host on which to deploy the new appliance>",
            "username": "root",
            "password": "<Password of the ESXi host root user. If left blank, or omitted, you will be prompted to enter it at the command console during template verification.>",
            "deployment.network": "VM Network",
            "datastore": "<A specific ESXi host datastore, or a specific datastore in a datastore cluster.>"
        },
        "appliance": {
            "thin.disk.mode": true,
            "deployment.option": "infrastructure",
            "name": "Platform-Services-Controller"
        },
        "network": {
            "ip.family": "ipv4",
            "mode": "static",
            "ip": "<Static IP address. Remove this if using dhcp.>",
            "dns.servers": [
                "<DNS Server IP Address. Remove this if using dhcp.>"
            ],
            "prefix": "<Network prefix length. Use only when the mode is 'static'. Remove if the mode is 'dhcp'. This is the number of bits set in the subnet mask; for instance, if the subnet mask is 255.255.255.0, there are 24 bits in the binary version of the subnet mask, so the prefix length is 24. If used, the values must be in the inclusive range of 0 to 32 for IPv4 and 0 to 128 for IPv6.>",
            "gateway": "<Gateway IP address. Remove this if using dhcp.>",
            "system.name": "<FQDN or IP address for the appliance. Remove this if using dhcp.>"
        },
        "os": {
            "password": "<Appliance root password; refer to --template-help for password policy. If left blank, or omitted, you will be prompted to enter it at the command console during template verification.>",
            "ssh.enable": false
        },
        "sso": {
            "password": "<vCenter Single Sign-On administrator password; refer to --template-help for password policy. If left blank, or omitted, you will be prompted to enter it at the command console during template verification.>",
            "domain-name": "vsphere.local",
            "site-name": "<vCenter Single Sign-On site name>"
        }
    },
    "ceip": {
        "description": {
            "__comments": [
                "++++VMware Customer Experience Improvement Program (CEIP)++++",
                "VMware's Customer Experience Improvement Program (CEIP) ",
                "provides VMware with information that enables VMware to ",
                "improve its products and services, to fix problems, ",
                "and to advise you on how best to deploy and use our ",
                "products. As part of CEIP, VMware collects technical ",
                "information about your organization's use of VMware ",
                "products and services on a regular basis in association ",
                "with your organization's VMware license key(s). This ",
                "information does not personally identify any individual. ",
                "",
                "Additional information regarding the data collected ",
                "through CEIP and the purposes for which it is used by ",
                "VMware is set forth in the Trust & Assurance Center at ",
                "http://www.vmware.com/trustvmware/ceip.html . If you ",
                "prefer not to participate in VMware's CEIP for this ",
                "product, you should disable CEIP by setting ",
                "'ceip.enabled': false. You may join or leave VMware's ",
                "CEIP for this product at any time. Please confirm your ",
                "acknowledgement by passing in the parameter ",
                "--acknowledge-ceip in the command line.",
                "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
            ]
        },
        "settings": {
            "ceip.enabled": true
        }
    }
}
~~~

A lot of the fields are self explanatory, just as `"hostname"`, `"datastore`", `"ip"`. The one that is not so obvious is:

* `"deployment.option":` - for an external PSC you need to leave this as `"infrastructure"`

You need to complete the other options as explained in the template. One thing that is missing from all the templates is defining NTP servers for the appliance. This has to be done in the `"os"` section. So my completed json file looked as so:

~~~ json
{
    "__version": "2.3.0",
    "__comments": "Sample template to deploy a Platform Services Controller appliance as the first instance in a new vCenter Single Sign-On domain on an ESXi host.",
    "new.vcsa": {
        "esxi": {
            "hostname": "esxi-01.corp.contoso.com",
            "username": "root",
            "password": "VMware1!",
            "deployment.network": "VM Network",
            "datastore": "datastore"
        },
        "appliance": {
            "thin.disk.mode": true,
            "deployment.option": "infrastructure",
            "name": "psc-01.corp.contoso.com"
        },
        "network": {
            "ip.family": "ipv4",
            "mode": "static",
            "ip": "10.10.1.50",
            "dns.servers": [
                "10.10.1.10","10.10.1.11"
            ],
            "prefix": "24",
            "gateway": "10.10.1.254",
            "system.name": "psc-01.corp.contoso.com"
        },
        "os": {
            "password": "VMware1!",
            "ssh.enable": false,
            "ntp.servers": [
                "0.us.pool.ntp.org","1.us.pool.ntp.org","2.us.pool.ntp.org","3.us.pool.ntp.org"
            ]
        },
        "sso": {
            "password": "VMware1!",
            "domain-name": "vsphere.local",
            "site-name": "First-Site"
        }
    },
    "ceip": {
        "description": {
            "__comments": [
                "++++VMware Customer Experience Improvement Program (CEIP)++++",
                "VMware's Customer Experience Improvement Program (CEIP) ",
                "provides VMware with information that enables VMware to ",
                "improve its products and services, to fix problems, ",
                "and to advise you on how best to deploy and use our ",
                "products. As part of CEIP, VMware collects technical ",
                "information about your organization's use of VMware ",
                "products and services on a regular basis in association ",
                "with your organization's VMware license key(s). This ",
                "information does not personally identify any individual. ",
                "",
                "Additional information regarding the data collected ",
                "through CEIP and the purposes for which it is used by ",
                "VMware is set forth in the Trust & Assurance Center at ",
                "http://www.vmware.com/trustvmware/ceip.html . If you ",
                "prefer not to participate in VMware's CEIP for this ",
                "product, you should disable CEIP by setting ",
                "'ceip.enabled': false. You may join or leave VMware's ",
                "CEIP for this product at any time. Please confirm your ",
                "acknowledgement by passing in the parameter ",
                "--acknowledge-ceip in the command line.",
                "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
            ]
        },
        "settings": {
            "ceip.enabled": true
        }
    }
}
~~~

That completes the PSC json file. Next is the vCSA json file. This is the one I used:

~~~ json
{
    "__version": "2.3.0",
    "__comments": "Sample template to deploy a vCenter Server Appliance with an external Platform Services Controller on an ESXi host.",
    "new.vcsa": {
        "esxi": {
            "hostname": "esxi-01.corp.contoso.com",
            "username": "root",
            "password": "VMware1!",
            "deployment.network": "VM Network",
            "datastore": "datastore"
        },
        "appliance": {
            "thin.disk.mode": true,
            "deployment.option": "management-small",
            "name": "vcsa-01.corp.contoso.com"
        },
        "network": {
            "ip.family": "ipv4",
            "mode": "static",
            "ip": "10.10.1.51",
            "dns.servers": [
                "10.10.1.10","10.10.1.11"
            ],
            "prefix": "24",
            "gateway": "10.10.1.254",
            "system.name": "vcsa-01.corp.contoso.com"
        },
        "os": {
            "password": "VMware1!",
            "ssh.enable": false,
            "ntp.servers": [
                "0.us.pool.ntp.org","1.us.pool.ntp.org","2.us.pool.ntp.org","3.us.pool.ntp.org"
            ]
        },
        "sso": {
            "password": "VMware1!",
            "domain-name": "vsphere.local",
            "platform.services.controller": "psc-01.corp.contoso.com",
            "sso.port": 443
        }
    }
}
~~~

So lets go through the sections. `"esxi"` is where we define how to connect to the ESXi host we are deploying to and then which datastore and network we are using. Next in the `"appliance"` section we need to define the `"deployment.option"`. This is the size of the appliance. There are various options depending on the number of host and VM's that are to be managed. You can lookup all the options on [docs.vmware.com](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.install.doc/GUID-457EAE1F-B08A-4E64-8506-8A3FA84A0446.html). I am using the `"management-small"` which means:

> Set to management-small if you want to deploy a vCenter Server Appliance with an external Platform Services Controller for up to 100 hosts and 1,000 virtual machines with the default storage size.

In `"network"` we set the static IP and DNS servers along with the DNS name of the appliance.  Again under `"os"` by default the template doesn't set the NTP servers so I added them in.  Lastly under `"sso"` we need to use the same SSO domain credentials defined in the PSC json file, such as the SSO password, domain-name and the name of the external PSC.

## Deploying the VM's

The utility to deploy the VM's using the CLI is located in `\vcsa-cli-installer\win32` and is called `vcsa-deploy.exe`. Open a command prompt and browse to that folder. First of all we want to verify everything is good with our json file. We can verify them using the tool:

~~~ winbatch
vcsa-deploy install --accept-eula --acknowledge-ceip --no-esx-ssl-verify --verify-only C:\Scripts\psc-01.json
~~~

This will verify the json is correct and will work. Next we want to deploy the PSC VM:

~~~ winbatch
vcsa-deploy install --accept-eula --acknowledge-ceip --no-esx-ssl-verify --log-dir=C:\Logs\PSC C:\Scripts\psc-01.json
~~~

we are accepting the EULA, acknowledges the Customer Experience Improvement Program participation, skips the SSL verification for the host, puts the log files somewhere easy to find, and finally the json file. It will deploy the VM and customise it according to your settings in the json file.

Next we want to verify and deploy the vCSA:

~~~ winbatch
vcsa-deploy install --accept-eula --acknowledge-ceip --no-esx-ssl-verify --verify-only C:\Scripts\vcsa-01.json
vcsa-deploy install --accept-eula --acknowledge-ceip --no-esx-ssl-verfy --log-dir=C:\Logs\VCSA C:\Scripts\vcsa-01.json
~~~

Once deployed you will be able to login to your functioning vCenter at https://vcsa-01.corp.contoso.com/vsphere-client and see it is part of the SSO domain from the PSC.

## Conclusion

The CLI installer is a powerful way of deploying your vCenter VM(s). Once you have a good json file you can deploy a vCenter and PSC using two command lines and have a record of the configuration that can be placed in source control. If you deploy a vCSA once every few years it may not be useful, but if you are a consultant deploying all the time you can really save yourself some time and clicks by using this method.

Have a look at the documentation for all of this at [docs.vmware.com](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.install.doc/GUID-C17AFF44-22DE-41F4-B85D-19B7A995E144.html).