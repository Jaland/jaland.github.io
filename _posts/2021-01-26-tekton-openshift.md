---
layout: post
title:  "Tekton(Openshift Pipeline) Pushing to quay.io"
tags: openshift tekton pipeline task
category: openshift
---

# Tekton Pipelines pushing to Quay.io

## Why am I starting with task (don't read this)

I know it may be strange that I am putting how to create a specific `task` for a tekton pipeline before putting a more general how to create a tekton pipeline but... well this is my blog. And I assume I am the only one who will ever read it!

Wait does that mean I am just justifying this to myself... er am I going crazy? Shh, Jamie you are perfectly sane, worst case you are just building a future legal defence.  

## Set Up Docker Config

Pipeline requires a docker config secret:
```
oc create secret docker-registry dockerconfigjson \
  --docker-username=jland \
  --docker-password=pass113 \
  --docker-email=jland@acme.com

```


## Create Task

Most of this was stolen from the tech preview of the openshift pipeline and I will note what changed below

```

```