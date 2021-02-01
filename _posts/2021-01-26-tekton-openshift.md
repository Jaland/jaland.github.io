---
layout: post
title:  "Tekton(Openshift Pipeline) Pushing to quay.io"
tags: openshift tekton pipeline task helm
category: openshift
---

# Tekton Pipelines pushing to Quay.io

## Why am I starting with task (don't read this)

I know it may be strange that I am putting how to create a specific `task` for a tekton pipeline before putting a more general how to create a tekton pipeline but... well this is my blog. And I assume I am the only one who will ever read it!

Wait does that mean I am just justifying this to myself... er am I going crazy? Shh, Jamie you are perfectly sane, worst case you are just building a future legal defence.  


## Pre-Reqs  

* An Openshift 4.6+ instance
* Openshift Pipelines operator installed (Can be found in the Operator Hub)
* Helm (If you want to use the pre-existing pipeline)

## Tekton Pipeline Example

I have created a Helm chart that will deploy a working pipeline with all the required resources for pushing to a container repository. But I will go over how to create the `Task` portion below.

https://github.com/Jaland/push-image-tekton-pipeline

## Pull Secret

Before we create the task we are going to need to create a pull `secret` so Openshift will have the credentials to pull/push to our repository.
---
**IMPORTANT**

If you are using a shared namespace make sure you understand pull [secrets](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)

Any credentials you push will be accessable by other people who share that repository, this is for demo purposes only.

---



If you would like to create a helm chart I found a very nice helper method on the [Creating Image Pull Secrets](https://helm.sh/docs/howto/charts_tips_and_tricks/) section of the Helm Tips and Tricks page.

Otherwise you can create our pull secret by running the following command:

#### **`secret/docker-registry`**
```
oc create secret docker-registry dockerconfigjson \
  --docker-server=quay.io \
  --docker-username=jland \
  --docker-password=pass113 \
  --docker-email=jland@acme.com 
```

## Task

I will like the entire task below since it is mostly taken from the `s2i` cluster task that is part of the Openshift Pipeline tech preview I will just go over the piece that changed.

If we start with the cluster task `java-s2i-8`.

First we need to create a new volume for the task that takes our dockerconfigjson and mounts it as `config.json`

```
    volumes:
    - name: quay-auth-secret
      secret:
        secretName: dockerconfigjson
        items:
          - key: .dockerconfigjson
            path: config.json
```

Then modify the `push` command to push to the `$(resources.outputs.image.url)` repo instead of the interanal one. (.i.e. `quay.io/username/repoName:v1`).

And mount the `quay-auth-secret volume` we created into `/etc/secret-volume`
```
    - command:
        - buildah
        - push
        - '--authfile'
        - /etc/secret-volume/config.json
        - $(resources.outputs.image.url)
      image: registry.redhat.io/rhel8/buildah
      name: push
      resources: {}
      securityContext:
        privileged: true
      volumeMounts:
        - mountPath: /var/lib/containers
          name: varlibcontainers
        - name: quay-auth-secret
          mountPath: /etc/secret-volume
          readOnly: true
```

## Task Example

#### **`Task/s2i-jboss-push-to-quay`**
```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: s2i-jboss-push-to-quay
spec:
  params:
    - default: .
      description: The location of the path to run s2i from
      name: PATH_CONTEXT
      type: string
    - default: 'true'
      description: >-
        Verify the TLS on the registry endpoint (for push/pull to a non-TLS
        registry)
      name: TLSVERIFY
      type: string
    - default: ''
      description: Additional Maven arguments
      name: MAVEN_ARGS_APPEND
      type: string
    - default: 'false'
      description: Remove the Maven repository after the artifact is built
      name: MAVEN_CLEAR_REPO
      type: string
    - default: ''
      description: The base URL of a mirror used for retrieving artifacts
      name: MAVEN_MIRROR_URL
      type: string
    - default: 'latest'
      description: Image Tag
      name: TAG
      type: string
  resources:
    inputs:
      - name: source
        type: git
    outputs:
      - name: image
        type: image
  steps:
    - args:
        - |-
          echo "MAVEN_CLEAR_REPO=$(params.MAVEN_CLEAR_REPO)" > env-file

          [[ '$(params.MAVEN_ARGS_APPEND)' != "" ]] &&
            echo "MAVEN_ARGS_APPEND=$(params.MAVEN_ARGS_APPEND)" >> env-file

          [[ '$(params.MAVEN_MIRROR_URL)' != "" ]] &&
            echo "MAVEN_MIRROR_URL=$(params.MAVEN_MIRROR_URL)" >> env-file

          echo "Generated Env file"
          echo "------------------------------"
          cat env-file
          echo "------------------------------"
      command:
        - /bin/sh
        - '-c'
      image: registry.redhat.io/ocp-tools-43-tech-preview/source-to-image-rhel8
      name: gen-env-file
      resources: {}
      volumeMounts:
        - mountPath: /env-params
          name: envparams
      workingDir: /env-params
    - command:
        - s2i
        - build
        - $(params.PATH_CONTEXT)
        - registry.access.redhat.com/jboss-eap-7/eap72-openshift
        - '--image-scripts-url'
        - 'image:///usr/local/s2i'
        - '--as-dockerfile'
        - /gen-source/Dockerfile.gen
        - '--environment-file'
        - /env-params/env-file
      image: registry.redhat.io/ocp-tools-43-tech-preview/source-to-image-rhel8
      name: generate
      resources: {}
      volumeMounts:
        - mountPath: /gen-source
          name: gen-source
        - mountPath: /env-params
          name: envparams
      workingDir: /workspace/source
    - command:
        - buildah
        - bud
        - '--tls-verify=$(params.TLSVERIFY)'
        - '--layers'
        - '-f'
        - /gen-source/Dockerfile.gen
        - '-t'
        - $(resources.outputs.image.url)
        - .
      image: registry.redhat.io/rhel8/buildah
      name: build
      resources: {}
      securityContext:
        privileged: true
      volumeMounts:
        - mountPath: /var/lib/containers
          name: varlibcontainers
        - mountPath: /gen-source
          name: gen-source
      workingDir: /gen-source
    - command:
        - buildah
        - push
        - '--authfile'
        - /etc/secret-volume/config.json
        - $(resources.outputs.image.url)
      image: registry.redhat.io/rhel8/buildah
      name: push
      resources: {}
      securityContext:
        privileged: true
      volumeMounts:
        - mountPath: /var/lib/containers
          name: varlibcontainers
        - name: quay-auth-secret
          mountPath: /etc/secret-volume
          readOnly: true
  volumes:
    - emptyDir: {}
      name: varlibcontainers
    - emptyDir: {}
      name: gen-source
    - emptyDir: {}
      name: envparams
    - name: quay-auth-secret
      secret:
        secretName: dockerconfigjson
        items:
          - key: .dockerconfigjson
            path: config.json
```
