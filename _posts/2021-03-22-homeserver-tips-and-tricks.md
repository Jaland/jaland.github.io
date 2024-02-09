---
layout: post
title:  "Home Server Tips and Tricks"
tags: homeserver linux
categories: homeserver
---

## My Current Setup

Since I recently upgraded the CPU in my personal desktop I found myself with almost enough parts to build out a small personal homeserver, just needed to get a Power Supply and a Case.

Going to use this page to relay how I fixed some of the hardships I found during my initial setup.

### Software:
*Current Operating System:* Fedora Server 33
*Cloud Softwrae:* [Code Ready Workspaces](https://code-ready.github.io/crc/)

### Hardware:
*CPU:* Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz
*Memory:* --8Gi-- 16Gi (Script did )
*Hard Dreive:* 1.8TB HHD

## Setting Up WiFI

In order to get my wifi boradband chip working, here are the steps I took. 

1. Find Latest Stable Release from RPM Fusion Nonfree rpm : http://download1.rpmfusion.org/nonfree/fedora/

2. Install rpmfusion-nonfree-release-stable rpm:

```
rpm -Uvh rpmfusion-nonfree-release-stable*rpm
```

3. Install Kmod-wl driver
```
sudo dnf install kmod-wl
```

4. Install wpa_supplicant

```
dnf install wpa_supplicant
```

5. Reboot

6. At this point `nmcli` sould show our wifi connection as disconnected

7. Manual connection initially:

    *  Turn on wifi radio `nmcli r wifi on`

    * Find avalible networks `nmcli d wifi list`

    * Connect to  network `nmcli device wifi <SSID> password <PASSWORD>`

    * Test with a basic `curl google.com` or whatever


8. Automagic connection on boot:

    * Add the following file to `/etc/sysconfig/network-scripts/ifcfg-<DEVICE_NAME>`


#### **`/etc/sysconfig/network-scripts/ifcfg-wlp4s0`**
```
TYPE=Wireless
DEVICE=<DEVICE_NAME>
ONBOOT=yes
ESSID='<SSID>'
```

    * Create a key-<DEVICE_NAME> in the same folder

#### **`/etc/sysconfig/network-scripts/key-wlp4s0`**
```
KEY=password
```