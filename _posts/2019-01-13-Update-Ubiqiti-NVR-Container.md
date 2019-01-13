---
layout: single
title: "Updating Ubiquiti NVR Container on a Synology"
date: 2019-01-13
category: [Other]
excerpt: "Updating the pducharme Docker Container on a Synology NAS"
---
# Introduction

I use [Ubiquiti UniFi cameras](https://www.ubnt.com/products/#unifivideo) around my home as the CCTV solution. I didn't want to shell out for the NVR since I had a Synology NAS with plenty of storage.

I use an image maintained by [pducharme](https://hub.docker.com/r/pducharme/unifi-video-controller/) and there are a few guides available on setup of this on a Synology. A good example is [here](https://community.ubnt.com/t5/UniFi-Video/Setup-Guide-Unifi-Video-in-Docker-on-Synology/m-p/2461750/highlight/true#M108835) on the Ubiquiti Forums. However I keep seeing people asking how to update to the latest version when already setup. I have written this post to help others and some self documentation for myself so I remember the commands when I perform the upgrade.

## Pre-requisites

This assumes you already have Docker running and the pducharme container working. The NVR UI will alert you when an upgrade is required.

The SSH service should be running on the Synology and you have a SSH client such as [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) available.

## Procedure

The main outline of the steps is:

1. SSH in and elevate permissions
2. Stop the NVR container
3. Remove the container
4. Remove the image
5. Startup the new container

SSH into the Synology and enter and elevated prompt with something like `sudo -i`.

First we need to list the running containers with the command `docker ps`:

~~~ bash
root@NAS-01:~# docker ps
CONTAINER ID        IMAGE                              COMMAND             CREATED             STATUS              PORTS                                                                                                                NAMES
822f33a4009c        pducharme/unifi-video-controller   "/run.sh"           5 days ago          Up 5 days           0.0.0.0:1935->1935/tcp, 0.0.0.0:6666->6666/tcp, 0.0.0.0:7080->7080/tcp, 0.0.0.0:7442-7447->7442-7447/tcp, 7004/udp   unifi-video
~~~

This gives us the container id for NVR - in this case `822f33a4009c`. We now need to stop this container with `docker stop`:

~~~ bash
root@NAS-01:~# docker stop 822f33a4009c
822f33a4009c
~~~

Now it's stopped time to remove it with `docker rm`:

~~~ bash
root@NAS-01:~# docker rm 822f33a4009c
822f33a4009c
~~~

Now the container is gone, time to find the image id using `docker image list`:

~~~ bash
root@NAS-01:~# docker image list
REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
pducharme/unifi-video-controller   latest              80c5675cbbf2        7 weeks ago         666MB
~~~

We have the image ID of `80c5675cbbf2` so we can now remove it:

~~~ bash
root@NAS-01:~# docker image rm 80c5675cbbf2
~~~

You may be worried that all the settings and recordings are gone but they are stored on a volume on the Synology.

Lastly let us download and start a new container with the latest build of the NVR from Docker Hub using `docker run`:

~~~ bash
root@NAS-01:~# docker run --restart always --name unifi-video --cap-add SYS_ADMIN --cap-add DAC_READ_SEARCH -p 1935:1935 -p 6666:6666 -p 7080:7080 -p 7442:7442 -p 7443:7443 -p 7444:7444 -p 7445:7445 -p 7446:7446 -p 7447:7447 -v /volume1/docker/unifi-video:/var/lib/unifi-video -e TZ=Europe/London -e PUID=99 -e PGID=100 -e DEBUG=1 --security-opt apparmor:unconfined pducharme/unifi-video-controller
~~~

Please note the part `-v /volume1/docker/unifi-video:/var/lib/unifi-video`. This is specific to my Synology and the volume\folder I setup for holding the NVR settings and recordings. Pleas change it to fir your setup.

Once the image is downloaded and the container is running the camera firmware is upgraded automatically so it will take a few minutes.

### Wrap Up

Using the NVR docker image was my first use of docker and containers and I Love how easy and quick it is to bring up a service or system. I am looking forward to seeing what other containers I can run on my Synology.

I hope this helps the Ubiquiti community on using the great image pducharme has made available.