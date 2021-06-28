---
layout: post
title:  "Openshift Installation on Fedora Server 33"
tags: openshift fedora crc
category: openshift
---

## CRC

Download CRC from https://cloud.redhat.com/openshift/create/local

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

## Other Documentation Documentation


Setting Up DNS:
https://access.redhat.com/documentation/en-us/red_hat_codeready_containers/1.24/html/getting_started_guide/networking_gsg#dns-configuration_gsg


Setting up VPN Server:
https://www.howtoforge.com/tutorial/how-to-install-openvpn-server-and-client-with-easy-rsa-3-on-centos-7/