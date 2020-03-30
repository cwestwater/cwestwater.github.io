---
layout: single
title: "Ansible and WinRM in a Workgroup"
date: 2018-12-20
category: [Ansible]
excerpt: "How to get WinRM working with Ansible in a Workgroup"
---
# Introduction

I have started learning Ansible with the help of various blogs and [Pluralsight](https://www.pluralsight.com/browse) videos. My focus is on using Ansible to configure Windows.

Ansible uses WinRM to connect to Windows servers and I was having some trouble connecting from my CentOS Ansible VM to either Windows 2012 R2 or 2016 that were standalone servers, so Workgroup and not joined to Active Directory.

After too many attempts to get Ansible to connect to the 2012 R2 VM I thought I would document how to get the connection going.

## Environment

This has been tested with the following:

* Ansible 2.7.4 running on CentOS 7. Server is called ansible
* Target server of Windows Server 2012 R2 fully patched. No configuration changes made to the OS. Server is called control
* Target server of Windows Server 2016 fully patched. No configuration changes made to the OS. Server is called server2
* All VMs nested in VMware Workstation 15. NAT on the NIC and Workstation handling DHCP
* Confirmed I can ping the two Windows servers from CentOS by name

Ansible has been setup with a simple inventory file:

~~~ yml
---
[servers]
server1
server2
~~~

Then the variables for connecting to the Windows servers:

~~~ yml
ansible_user: Administrator
ansible_password: "Password1"
ansible_port: 5985
ansible_connection: winrm
~~~

So connect to Windows over `WinRM` on port `5985` using the username/password of `Administrator/Password`

## Initial Test

To test functionality I am going to use the Ansible module [win_ping](https://docs.ansible.com/ansible/latest/modules/win_ping_module.html). As the documentation says this simply checks management connectivity of a windows host.

Running ansible gives the following result:

~~~ bash
[ansible@control]$ ansible servers -i inventory.yml -m win_ping
server1 ¦ UNREACHABLE! => {
    "changed": false,
    "msg": "plaintext: the specified credentials were rejected by the server",
    "unreachable": true
}
server2 ¦ UNREACHABLE! => {
    "changed": false,
    "msg": "plaintext: the specified credentials were rejected by the server",
    "unreachable": true
}
~~~

So something is wrong here. It's not the credentials as I know they are correct. It must be some other authentication issue.

## The Solution

The root of the problem is that we are sending credentials in plaintext over the unencrypted WinRM port 5985. As this was a lab I just wanted to easily test, so I was willing to do this. **Do not do this in production!**

The fix is a couple of WinRM configuration changes in the Windows servers. In an elevated command prompt type the following commands:

~~~ dosbatch
C:\Windows\system32>winrm set winrm/config/service/auth @{Basic="true"}
Auth
    Basic = true
    Kerberos = true
    Negotiate = true
    Certificate = false
    CredSSP = false
    CbtHardeningLevel = Relaxed

C:\Windows\system32>winrm set winrm/config/service @{AllowUnencrypted="true"}
Service
    RootSDDL = O:NSG:BAD:P(A;;GA;;;BA)(A;;GR;;;IU)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)
    MaxConcurrentOperations = 4294967295
    MaxConcurrentOperationsPerUser = 1500
    EnumerationTimeoutms = 240000
    MaxConnections = 300
    MaxPacketRetrievalTimeSeconds = 120
    AllowUnencrypted = true
    Auth
        Basic = true
        Kerberos = true
        Negotiate = true
        Certificate = false
        CredSSP = false
        CbtHardeningLevel = Relaxed
    DefaultPorts
        HTTP = 5985
        HTTPS = 5986
    IPv4Filter = *
    IPv6Filter = *
    EnableCompatibilityHttpListener = false
    EnableCompatibilityHttpsListener = false
    CertificateThumbprint
    AllowRemoteAccess = true
~~~

The first command sets WinRM to Basic Authentication and the second allows unencrypted traffic.

Running Ansible again:

~~~ dosbatch
[ansible@control]$ ansible servers -i inventory.yml -m win_ping
server1 ¦ SUCCESS! => {
    "changed": false,
    "ping": "pong"
}
server2 ¦ SUCCESS! => {
    "changed": false,
    "ping": "pong"
}
~~~

We can now access the servers over WinRM.

## Wrap Up

As I said above this is not how to run this in production. Normally you would use encrypted traffic to a domain joined server using a proper authentication method such as Kerberos or CredSSP.

In the next post I am going to look at further authentication options for Ansible and WinRM.
