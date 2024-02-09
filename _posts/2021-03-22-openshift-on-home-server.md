---
layout: post
title:  "Openshift Installation on Fedora Server 33"
tags: openshift fedora crc
categories: openshift
---

> Note: Still having issues, if you find this article don't use it.

## Intro

I am going to go over how I used an old Gaming Laptop in order to install CRC that was accessible throughout the house.

### Prereqs

* Existing hardware that can run CRC, [minimum requirements](https://access.redhat.com/documentation/en-us/red_hat_codeready_containers/1.0/html/getting_started_guide/getting-started-with-codeready-containers_gsg#minimum-system-requirements_gsg)
* SSH Setup on the server

## Download CRC

Download CRC from https://cloud.redhat.com/openshift/create/local
<sub>I found the easiest way to do tis is just download on your main computer and push to the server using scp</sub>

Play the `crc` command inside you `/bin/` folder

Run `crc setup` then `crc start`

### Open the required ports on your firewall

```
sudo systemctl start firewalld
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=6443/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
sudo systemctl restart firewalld
sudo semanage port -a -t http_port_t -p tcp 6443
```

## Setup Client Machine

### Update DNS masq config

Inside of `/etc/dnsmasq.conf` I added the following lines:
```
address=/apps-crc.testing/192.168.1.170
address=/api.crc.testing/192.168.1.170
server=/apps-crc.testing/192.168.1.170
server=/crc.testing/192.168.1.170
```

### Start DNS Masq

```
sudo systemctl start dnsmasq
sudo systemctl enable dnsmasq
```


## Other Documentation Documentation

Directions I followed (some changes I mention in setup client machine section):
https://cloud.redhat.com/blog/accessing-codeready-containers-on-a-remote-server/


Setting Up DNS:
https://access.redhat.com/documentation/en-us/red_hat_codeready_containers/1.24/html/getting_started_guide/networking_gsg#dns-configuration_gsg


Setting up VPN Server:
https://www.howtoforge.com/tutorial/how-to-install-openvpn-server-and-client-with-easy-rsa-3-on-centos-7/