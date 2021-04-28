---
layout: post
title:  "Install helm on a Dockerfile"
tags: docker helm openshift
category: docker
---

## Install helm on a Dockerfile

Useful clip for creating a Jenkins Agent that includes Helm. Can replace the version with whatever is the latest.

```
# Install helm
RUN curl -fsSL -o /tmp/helm.tar.gz 	https://get.helm.sh/helm-v3.5.3-linux-amd64.tar.gz && \
    tar -zxvf /tmp/helm.tar.gz -C /tmp && \
    mv /tmp/linux-amd64/helm /usr/local/bin/helm && \
    rm /tmp/helm.tar.gz && rm -rf /tmp/linux-amd64
```