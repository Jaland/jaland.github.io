---
layout: post
title:  "Creating an AWS CLI Container"
tags: AWS
category: aws cli openshift containers
---

# Creating an AWS CLI Container

I have had multiple instances lately where I have needed to run AWS cli commands on Openshift. Unfortunately there is not (yet) a basic container in the Red Hat Registry for doing this. 

AWS has created a container found [here](https://hub.docker.com/layers/amazon/aws-cli/2.8.12/images/sha256-af4671e6f3c583e9f76778a0ec7752b26a628edb0452a3e60687fd23aa2631e8?context=explore), but when trying to use it on Openshift's secure environment there are permission issue.

Which is why I created my own version of the container that fixes the issues and can be found [here](https://quay.io/repository/jland/aws-cli?tab=tags). I have not built any automation around keeping this up to date...yet... but writing this article in the meantime to remember how I can rebuild the container file. And how to use the image for running CLI commands.

 > **Note to self:** Build some automation dummy!

## Containerfile

Containerfile:

```dockerfile
from amazon/aws-cli:latest

RUN mkdir -p /.aws && \
    chgrp -R 0 /.aws && \
    chmod -R g=u /.aws && \
    chgrp -R 0 /home && \
    chmod -R g=u /home

WORKDIR /home
```

## Using the Image

Basic example for using the image:


```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: example-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      name: example-app
      labels:
        app: example-app
    spec:
      containers:
        - name: my-application-deployment
          image: 'quay.io/jland/aws-cli:latest'
          command:
            - /bin/bash
            - '-c'
            - '--'
          args:
            - aws sts get-caller-identity && while true; do sleep 30; done
```

## Hot Tips

You can make changes to how the CLI functions using a set of environment variables found here:

https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html