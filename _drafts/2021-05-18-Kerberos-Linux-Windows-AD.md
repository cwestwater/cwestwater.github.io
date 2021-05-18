---
layout: single
classes: wide
title: "AD Authentication in Ubuntu"
date: 2021-05-18
category: [Microsoft]
excerpt: "How to setup AD Authentication in Ubuntu and a tip to ensure it works"
---
## Introduction

I am starting to get back into Ansible and wanted to setup AD Authentication from the control node (Ubuntu 20.04) to my Windows 2019 domain. The Ubuntu VM has a DHCP IP from the Domain Controller and DNS resolution is working correctly:

~~~bash
cwestwater@ubuntu:~$ nslookup dc01
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	dc01.corp.contoso.com
Address: 10.10.1.10
~~~

I read a couple of articles on how to do this but could not get authentication to work. I finally saw my error and so thought I'd document it here.

## Install Kerberos package in Ubuntu

The first thing to do it install the package to enable Kerberos authentication:

~~~bash
cwestwater@ubuntu:~$ sudo apt-get install krb5-user
~~~

This installs the package then prompts for you to enter your default Kerberos realm aka the domain name. In my lab this is `corp.contoso.com`:

![krb5-user Setup]({{ site.url }}/assets/images/krb5-user.png)

I then tested by trying to authenticate using the `kinit` command and passing my admin credential:

~~~bash
cwestwater@ubuntu:~$ kinit a-cwestwater
Password for a-cwestwater@corp.contoso.com:
kinit: KDC reply did not match expectations while getting initial credentials
~~~

So we can see it it picking up the domain in the username ok, but not throwing an error.

## Kerberos Configuration

Next I found there was a configuration file for setup of Kerberos called `krb5.conf`. I did some research using links such as to see if I was missing something:

- [Configure realms under krb5.conf file for using AD Authentication for RHEL VMs](https://access.redhat.com/discussions/3479491)
- [MIT documenation](https://web.mit.edu/kerberos/krb5-1.5/krb5-1.5.4/doc/krb5-admin/krb5_002econf.html#krb5_002econf)
- [krb5.conf(5) - Linux man page](https://linux.die.net/man/5/krb5.conf)

Opening this file I could see under `[libdefaults]` the domain name I entered above:

~~~bash
[libdefaults]
        default_realm = corp.contoso.com
~~~

Next I started looking at the options for the `[libdefaults]` section and settled on this:

~~~bash
[libdefaults]
        default_realm = corp.contoso.com
        dns_lookup_realm = false
        ticket_lifetime = 24h
        renew_lifetime = 7d
        forwardable = true
        rdns = false
~~~

What do these options mean?

- default_realm is what was entered in the first step when the Kerberos package was install. This is the Active Directory domain
- dns_lookup_realm set to false specifies that DNS TXT records should not be used to determine the domain of a host
- ticket_lifetime is time a ticket (aka authentication) is valid for
- renew_lifetime is the renewable lifetime for the authentication ticket
- forwardable let you forward the authentication ticket
- rdns prevent the use of reverse DNS resolution when translating hostnames into service principal names (more secure)

I'm not a Kerberos expert but the options seem reasonable so I used them. More details for each option are in the links above if you want to delve deeper. I tried again to get a kerberos ticket:

~~~bash
cwestwater@ubuntu:~$ kinit a-cwestwater
Password for a-cwestwater@corp.contoso.com:
kinit: KDC reply did not match expectations while getting initial credentials
~~~

Still the same issue.

## The Solution

So after extensive Googling and changes to the configuration file I stumbled upon this post: [Linux: Kerberos authentification against Windows Active Directory](https://michlstechblog.info/blog/linux-kerberos-authentification-against-windows-active-directory/). by Michael Albert. I noticed my fatal mistake:

> If the “KDC reply did not match expectations while getting initial credentials” error occurs, check your /etc/krb5.conf. Ensure that all Realm names are in upper case letters.

I changed my config file [libdefaults] section to this:

~~~bash
[libdefaults]
        default_realm = CORP.CONTOSO.COM
        dns_lookup_realm = false
        ticket_lifetime = 24h
        renew_lifetime = 7d
        forwardable = true
        rdns = false
~~~

and then tried authenticating again:

~~~bash
cwestwater@ubuntu:~$ kinit a-cwestwater
Password for a-cwestwater@corp.contoso.com:
~~~

No error message but nothing was returned. Was I successful or not? There is a command `klist` that can show your current tokens:

~~~bash
cwestwater@ubuntu:~$ klist
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: a-cwestwater@CORP.CONTOSO.COM

Valid starting     Expires            Service principal
18/05/21 20:27:31  19/05/21 06:27:31  krbtgt/CORP.CONTOSO.COM@CORP.CONTOSO.COM
	renew until 25/05/21 20:27:28
~~~

That looked good to me. However I wanted to test to make sure authentication was working as I hoped. On Michaels post he shows how to connect to a Windows server once you have a Kerberos ticket using a package called `rpcclient`:

~~~ bash
cwestwater@ubuntu:~$ rpcclient dc01.corp.contoso.com -k
rpcclient $> srvinfo
	DC01.CORP.CONTOWk Sv PDC Tim NT
	platform_id     :	500
	os version      :	10.0
	server type     :	0x80102b
rpcclient $> getusername
Account Name: a-cwestwater, Authority Name: corp
rpcclient $> getusername
Account Name: a-cwestwater, Authority Name: corp
rpcclient $>
~~~

First we connect to the my Domain Controller `dc01.corp.contoso.com` using `rpcclient`. The `-k` [option](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) makes it use Kerberos for authentication. You can see we are connected correctly. Then at the `rpcclient` prompt I ran a `getusername` as a further check. It all looks good now.

## Wrap Up

In my defence I'm a Windows guy and so not worried that much about case sensitivity. I do however when using Linux just make everything lower case to stay consistent. That was my downfall this time!

I hope this helps someone else experiencing the same problem.
