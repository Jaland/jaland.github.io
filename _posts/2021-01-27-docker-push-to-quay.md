---
layout: post
title:  "Pull image and push to Quay"
tags: docker quay
categories: docker
---

# **PTP** - Pull Tag Push


1. Pull image `bitnami/nginx`:

```
docker pull bitnami/nginx:latest
```

2. Tag the image for quay:

```
docker tag bitnami/nginx quay.io/jland/<IMAGE NAME>:<IMAGE TAG>
```


3. Push to `quay.io`

```
docker push quay.io/jland/poc:1.0
```