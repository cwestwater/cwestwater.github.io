---
layout: single
title: "Updating Ubuntu in WSL with Kaspersky Installed"
date: 2018-12-02
category: [Microsoft]
excerpt: "How to resolve an updating issue in Ubuntu on Windows 10 that has Kaspersky installed"
---
# Introduction

I recently noticed a problem updating my Ubuntu install on Windows Subsystem for Linux (WSL). When I tried to update the install I kept getting:

~~~ bash
cwestwater@CWESTWATER-P500:~$ sudo apt update && sudo apt upgrade
[sudo] password for cwestwater:
Err:1 http://archive.ubuntu.com/ubuntu bionic InRelease
  Connection failed [IP: 91.189.88.161 80]
Err:2 http://security.ubuntu.com/ubuntu bionic-security InRelease
  Connection failed [IP: 91.189.88.152 80]
Err:3 http://archive.ubuntu.com/ubuntu bionic-updates InRelease
  Connection failed [IP: 91.189.88.149 80]
Err:4 http://archive.ubuntu.com/ubuntu bionic-backports InRelease
  Connection failed [IP: 91.189.88.161 80]
Reading package lists... Done
Building dependency tree
Reading state information... Done
All packages are up to date.
W: Failed to fetch http://archive.ubuntu.com/ubuntu/dists/bionic/InRelease  Connection failed [IP: 91.189.88.161 80]
W: Failed to fetch http://archive.ubuntu.com/ubuntu/dists/bionic-updates/InRelease  Connection failed [IP: 91.189.88.149 80]
W: Failed to fetch http://archive.ubuntu.com/ubuntu/dists/bionic-backports/InRelease  Connection failed [IP: 91.189.88.161 80]
W: Failed to fetch http://security.ubuntu.com/ubuntu/dists/bionic-security/InRelease  Connection failed [IP: 91.189.88.152 80]
W: Some index files failed to download. They have been ignored, or old ones used instead.
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following package was automatically installed and is no longer required:
  libfreetype6
Use 'sudo apt autoremove' to remove it.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
~~~

Here is how I get round the issue.

## Troubleshooting steps

I was still running the original Beta version of WSL so I decided to remove all traces of it and reinstall. Unfortunately I still had the same problem.

Next I did some of the standard things like pinging the IP address, going to the ubuntu archive site in Chrome, and trying to update an Ubuntu install on a VM in Workstation. All of those worked. After that I resorted to Google.

After a lot of searching I found out Kaspersky could be the issue. I have Kaspersky 19 installed on my PC so I started to investigate that.

## Kaspersky Issue

First thing I did was to stop Kaspersky altogether and check I could update. Running `sudo apt update && sudo apt upgrade` resulted in:

~~~ bash
cwestwater@CWESTWATER-P500:~$ sudo apt update && sudo apt upgrade
Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [83.2 kB]
Hit:2 http://archive.ubuntu.com/ubuntu bionic InRelease
Get:3 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:4 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:5 http://archive.ubuntu.com/ubuntu bionic/universe amd64 Packages [8570 kB]
Get:6 http://security.ubuntu.com/ubuntu bionic-security/main amd64 Packages [209 kB]
Get:7 http://security.ubuntu.com/ubuntu bionic-security/main Translation-en [82.1 kB]
Get:8 http://security.ubuntu.com/ubuntu bionic-security/universe amd64 Packages [99.1 kB]
Get:9 http://security.ubuntu.com/ubuntu bionic-security/universe Translation-en [55.5 kB]
Get:10 http://security.ubuntu.com/ubuntu bionic-security/multiverse amd64 Packages [1440 B]
Get:11 http://security.ubuntu.com/ubuntu bionic-security/multiverse Translation-en [996 B]
Get:12 http://archive.ubuntu.com/ubuntu bionic/universe Translation-en [4941 kB]
Get:13 http://archive.ubuntu.com/ubuntu bionic/multiverse amd64 Packages [151 kB]
Get:14 http://archive.ubuntu.com/ubuntu bionic/multiverse Translation-en [108 kB]
Get:15 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 Packages [444 kB]
Get:16 http://archive.ubuntu.com/ubuntu bionic-updates/main Translation-en [167 kB]
Get:17 http://archive.ubuntu.com/ubuntu bionic-updates/restricted amd64 Packages [6992 B]
Get:18 http://archive.ubuntu.com/ubuntu bionic-updates/restricted Translation-en [3076 B]
Get:19 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 Packages [581 kB]
Get:20 http://archive.ubuntu.com/ubuntu bionic-updates/universe Translation-en [158 kB]
Get:21 http://archive.ubuntu.com/ubuntu bionic-updates/multiverse amd64 Packages [6364 B]
Get:22 http://archive.ubuntu.com/ubuntu bionic-updates/multiverse Translation-en [3356 B]
Get:23 http://archive.ubuntu.com/ubuntu bionic-backports/universe amd64 Packages [3260 B]
Get:24 http://archive.ubuntu.com/ubuntu bionic-backports/universe Translation-en [1480 B]
Fetched 15.8 MB in 6s (2483 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
114 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following package was automatically installed and is no longer required:
  libfreetype6
Use 'sudo apt autoremove' to remove it.
The following NEW packages will be installed:
  libuv1
The following packages will be upgraded:
  apparmor apport apt apt-utils base-files bind9-host bsdutils cloud-init cloud-initramfs-copymods
  cloud-initramfs-dyn-netconf console-setup console-setup-linux cryptsetup cryptsetup-bin curl distro-info-data
  dnsutils dpkg fdisk friendly-recovery gcc-8-base gettext-base git git-man initramfs-tools initramfs-tools-bin
  initramfs-tools-core keyboard-configuration kmod libapparmor1 libapt-inst2.0 libapt-pkg5.0 libbind9-160 libblkid1
  libcryptsetup12 libcurl3-gnutls libcurl4 libdns-export1100 libdns1100 libfdisk1 libgcc1 libglib2.0-0 libglib2.0-data
  libirs160 libisc-export169 libisc169 libisccc160 libisccfg160 libkmod2 libldap-2.4-2 libldap-common liblwres160
  liblxc-common liblxc1 libmount1 libmspack0 libnss-systemd libpam-systemd libparted2 libplymouth4 libpython3-stdlib
  libpython3.6 libpython3.6-minimal libpython3.6-stdlib libsmartcols1 libstdc++6 libsystemd0 libudev1 libuuid1
  libx11-6 libx11-data libxml2 lshw lxcfs lxd lxd-client man-db mount networkd-dispatcher open-iscsi open-vm-tools
  openssh-client openssh-server openssh-sftp-server overlayroot parted plymouth plymouth-theme-ubuntu-text
  python-apt-common python3 python3-apport python3-apt python3-distupgrade python3-gdbm python3-minimal
  python3-problem-report python3-requests python3-software-properties python3-update-manager python3.6
  python3.6-minimal software-properties-common sosreport systemd systemd-sysv tzdata ubuntu-keyring
  ubuntu-release-upgrader-core udev unattended-upgrades update-manager-core update-notifier-common util-linux
  uuid-runtime
114 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 42.7 MB of archives.
After this operation, 781 kB of additional disk space will be used.
Do you want to continue? [Y/n] n
Abort.
~~~

Success! Kaspersky is the smoking gun. Ideally I don't want to completely stop AV whenever I want to update Ubuntu so I looked deeper into Kaspersky.

I had a hunch it was something to do with the Network settings in Kaspersky. So I had a look under `Settings...Additional...Network`. I started hitting all the sections disabling settings until I hit `Monitored ports`:

![Kaspersky 01]({{ site.url }}/assets/images/Kaspersky-01.png)
![Kaspersky 02]({{ site.url }}/assets/images/Kaspersky-02.png)
![Kaspersky 03]({{ site.url }}/assets/images/Kaspersky-03.png)

Once I got into the Network ports I saw there are a bunch of ports it is monitoring. As the output above shows `apt` is looking at `http` addresses so I disabled that monitoring:

![Kaspersky 04]({{ site.url }}/assets/images/Kaspersky-04.png)

and updating Ubuntu worked.

## Wrap Up

It's not ideal, but this seems to be the workaround. Whenever I am updating Ubuntu on WSL I will disable `http` monitoring in Kaspersky. I'm going to raise a support case with Kaspersky so hopefully they will fix this issue in a future release. Hope this helps someone out there and saves them some time.