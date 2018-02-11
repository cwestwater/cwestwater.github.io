---
layout: single
title: "vCSA Root Disk Space Issue Caused by dnsmasq"
date: 2018-02-11
category: [VMware]
excerpt: "How to fix a vCSA 6.0 that has run out of space due to large dnsmasq.log files"
---
# Introduction

I was having problems with my vCSA (6.0 Update 3d) such as not being able to login to the client. I went to the Appliance Management Interface and the Health was showing as critical. The cause was a couple of very large dnsmasq.log files. It took a couple of blog posts and a KB articles to fix, so I thought I would write up a consolidated blog post of the fix.

## Investigation

First of all I connected to the shell of the vCSA and then ran the command `df -h` to show the space used on each partition in human readable format:

~~~ bash
vcsa-01:~ # df -h
Filesystem                            Size  Used Avail Use% Mounted on
/dev/sda3                              11G   11G     0 100% /
udev                                  7.9G  164K  7.9G   1% /dev
tmpfs                                 7.9G   40K  7.9G   1% /dev/shm
/dev/sda1                             128M   38M   84M  31% /boot
/dev/mapper/core_vg-core               50G   18G   30G  38% /storage/core
/dev/mapper/log_vg-log                 20G   18G  1.5G  92% /storage/log
/dev/mapper/db_vg-db                  9.9G  812M  8.6G   9% /storage/db
/dev/mapper/dblog_vg-dblog            5.0G  603M  4.1G  13% /storage/dblog
/dev/mapper/seat_vg-seat               32G  2.4G   28G   8% /storage/seat
/dev/mapper/netdump_vg-netdump        3.0G   18M  2.8G   1% /storage/netdump
/dev/mapper/autodeploy_vg-autodeploy   12G  154M   12G   2% /storage/autodeploy
/dev/mapper/invsvc_vg-invsvc          9.9G  272M  9.1G   3% /storage/invsvc
~~~

You can see the partition `/dev/sda3` is out of space. I need to find out what was consuming all the space. Browsing through the disks I found in `/var/log`:

~~~ bash
vcsa-01:/var/log # ls -lh
total 4.6G
-rw-r----- 1 dnsmasq dnsmasq  17M Feb  4 08:32 dnsmasq.log
-rw-r----- 1 dnsmasq root    844M Jan 28 07:45 dnsmasq.log-20180128
-rw-r----- 1 dnsmasq dnsmasq 3.7G Feb  2 16:19 dnsmasq.log-20180204
~~~

Over 4.5GB of old log files!

## Immediate fix

I had two choices to immediately fix the issue. I could delete the log files or expand the drive. The least risky was to delete the log files but as the vCSA is going away soon due to a v6.5 deployment, I decided to do both and learn something new. I would **always** advise to do a snapshot of the appliance before you make any changes, but as we are expanding drives you can't do it with a snapshot in place. Expand the drive first then snapshot.

There are eleven disks on a v6.0 vCSA. William Lam has a [blog post](https://www.virtuallyghetto.com/2015/03/multiple-vmdks-in-vcsa-6-0.html) giving details. The disk we need to expand is VMDK1 which is by default 12GB. Use a client to expand the disk to say 15GB. If a disk is expanded you can normally use `lvm autogrow` to [expand the disk](https://www.virtuallyghetto.com/2015/02/increasing-disk-capacity-simplified-with-vcsa-6-0-using-lvm-autogrow.html) but in this case VMDK1 is split into three partitions and only `/dev/sda3` is full. You can see this by using the `lsblk` command:

~~~ bash
nor-vc-01:~ # lsblk
NAME                              MAJ:MIN RM   SIZE RO MOUNTPOINT
sda                                 8:0    0    12G  0 
├─sda1                              8:1    0   132M  0 /boot
├─sda2                              8:2    0     1G  0 [SWAP]
└─sda3                              8:3    0  10.9G  0 /
sdb                                 8:16   0   1.3G  0 
sdc                                 8:32   0    30G  0 
└─swap_vg-swap1 (dm-8)            253:8    0    30G  0 [SWAP]
sdd                                 8:48   0    50G  0 
└─core_vg-core (dm-7)             253:7    0    50G  0 /storage/core
sde                                 8:64   0    20G  0 
└─log_vg-log (dm-6)               253:6    0    20G  0 /storage/log
sdf                                 8:80   0    10G  0 
└─db_vg-db (dm-5)                 253:5    0    10G  0 /storage/db
sdg                                 8:96   0     5G  0 
└─dblog_vg-dblog (dm-4)           253:4    0     5G  0 /storage/dblog
sdh                                 8:112  0    32G  0 
└─seat_vg-seat (dm-3)             253:3    0    32G  0 /storage/seat
sdi                                 8:128  0     3G  0 
└─netdump_vg-netdump (dm-2)       253:2    0     3G  0 /storage/netdump
sdj                                 8:144  0    12G  0 
└─autodeploy_vg-autodeploy (dm-1) 253:1    0    12G  0 /storage/autodeploy
sdk                                 8:160  0    10G  0 
└─invsvc_vg-invsvc (dm-0)         253:0    0    10G  0 /storage/invsvc
sr0                                11:0    1  1024M  0 
fd0                                 2:0    1     4K  0 
~~~

`sda` is VMDK1 on the vCSA and it is `sda3` that is full.

I found an excellent [blog post](https://blog.mwpreston.net/2015/10/05/resizing-the-root-partition-of-the-vcenter-server-appliance-vcsa/) from [Mike Preston](https://twitter.com/mwpreston) that explained how to expand the disk.

**These next steps can be dangerous! PROCEED WITH CAUTION!**

I needed to use `fdisk` to rewrite the partition table. This is done by typing the commands:

~~~ bash
fdisk /dev/sda
d # d to delete a partition
3 # select partition 3 sda3

n # new
p # new partition
3 # choose partition 3

<Enter> # select default value at First sector prompt
<Enter> # select default value at Last sector prompt

a # make partition bootable
3 # select partition 3 to be be bootable
w # write the changes and exit
~~~

The output looks like this:

~~~ bash
vcsa-01:~ # fdisk /dev/sda

Command (m for help): d
Partition number (1-4): 3

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4, default 3): 3
First sector (2377728-31457279, default 2377728):
Using default value 2377728
Last sector, +sectors or +size{K,M,G} (2377728-31457279, default 31457279):
Using default value 31457279

Command (m for help): a
Partition number (1-4): 3

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
~~~

I naturally ran `df -h` to check the space but it was not expanded. Look at the Warning at the end of the output. The new partition table will not be active until after a reboot.

After reboot I ran `df -h` again but it was still the same size. Following onto step 3 of Mike's post I saw I needed to extend the filesystem (all I had done was alter partition tables). I used the command `resize2fs /dev/sda3`:

~~~ bash
vcsa-01:~ # resize2fs /dev/sda3
resize2fs 1.41.9 (22-Aug-2009)
Filesystem at /dev/sda3 is mounted on /; on-line resizing required
old desc_blocks = 1, new_desc_blocks = 1
Performing an on-line resize of /dev/sda3 to 3634944 (4k) blocks.
The filesystem on /dev/sda3 is now 3634944 blocks long.
~~~

and checking using `df -h`:

~~~ bash
vcsa-01:~ # df -h
Filesystem                            Size  Used Avail Use% Mounted on
/dev/sda3                              14G  11G  3G    51% /
~~~

Success! Thanks Mike for the excellent post.

I may have fixed the immediate problem, but it was likely to happen again.

The other, easier and less risky way to fix the issue was to delete the `dnsmasq.log` files:

~~~ bash
vcsa-01:/var/log # rm dnsmasq.log-20180204
vcsa-01:/var/log # rm dnsmasq.log-20180128
~~~

## Prevention

The next day I started looking into why the archived `dnsmasq.log` files where so large. `dnsmasq` is a [lightweight DHCP and caching DNS server](http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html) so I didn't know why it was logging so much. It just so happened I was checking Twitter and noticed [this conversation](https://twitter.com/eck79/status/961370101193805824) between [Justin Bias](https://twitter.com/vaerex) and [Adam Eckerle](https://twitter.com/eck79) which was exactly the issue I was having. What a coincidence that I saw this the day I was researching the issue. Adam really is a KB search wizard as I didn't find the referenced KB article either.

The KB article is [root partition on the vCenter Server Appliance is full due to dnsmasq.log files (52258)](https://kb.vmware.com/s/article/52258). This article details the workaround as there is no permanent fix and is a known issue. The workaround is in two parts.

The first part is to modify the log rotation options for `dnsmasq`. This is done by using `vi` to edit `/etc/logrotate.d/dnsmasq` to change it to match (I've put a comment on the lines you need to change):

~~~ bash
/var/log/vmware/dnsmasq.log {
nodateext # added to file
daily # changed from weekly
missingok
notifempty
compress #changed from delaycompress
maxsize 5M # added to file
rotate 5 # added to file
sharedscripts
postrotate
[ ! -f /var/run/dnsmasq.pid ] || kill -USR2 `cat /var/run/dnsmasq.pid`
endscript
create 0640 dnsmasq dnsmasq
}
~~~

Next is to move the `dnsmasq.log` files to the correct disk. Use `vi` to edit `/etc/dnsmasq.conf` and change the following line to match:

~~~ bash
log-facility=/var/log/vmware/dnsmasq.log
~~~

Finally restart the `dnsmasq` service:

~~~ bash
vcsa-01:~ # service dnsmasq restart
Shutting name service masq caching server
Starting name service masq caching server
~~~

Since then the `dnsmasq.log` files have been under control.

# Conclusion

The real purpose of this post was the highlight the KB article for controlling the `dnsmasq` log files. If it wasn't for Adam Eckerle Tweet I am not sure I would have found the article - and I consider my Google searching skills pretty good. Also thanks to Mike Preston for the new knowledge in expanding the `/dev/sda3` partition.

This is the third time I have had issues with disk space in a vCSA v6.0 so please be mindful of the partitions filling up and giving you problems. Monitor them closely and in particular the `/storage/log` and `/storage/db` disks.