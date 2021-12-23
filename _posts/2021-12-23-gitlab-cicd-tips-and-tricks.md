---
layout: post
title:  "Gitlab CI/CD Tips and Tricks"
tags: tekton openshift pipelines cicd
category: tekton
---

# Intro

The Gitlab CI/CD has been a surprisingly powerful and useful tool. I unfortunately did not get the opportunity to do the initial setup of the tool and connect it to our Kubernetes cluster but it looks like it was a pretty simple use of an operator. 

In order to enable this on the gitlab side go into your gitlab setting and turn on the cicd under `Settings -> General -> Visibility and Features -> CI/CD` 

# .gitlab-ci.yaml

Having the .gitlab-ci.yaml file in your base directory is how you define how you want your pipeline to run. 


#### **`.gitlab-ci.yaml`**
``` yaml
variables:
  ## This allows us to override the default service account we use
  ## NOTE: This must be enabled on the Kubernetes/Openshift side
  KUBERNETES_SERVICE_ACCOUNT_OVERWRITE: pipeline
stages:
  - "hello-world"

default:
  # Defines the image we want to use when running our scripts below
  image: image-registry.openshift-image-registry.svc:5000/openshift/openshift-tools:latest
  # This tag is used to specify the the Runner on the openshift side we want to use.
  tags:
    - "openshift"

hello-world:
  stage: hello-world
  script:
    - |
      echo "Hello-World"
  rules:
    # `when: never` means we will never run on a push to the default(main) branch even if other rules pass
    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
      when: never
    # This means we will only run our pipeline if there are changes to files under `folder`
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event" && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == $CI_DEFAULT_BRANCH'
      changes:
        - "folder/*"
```


## Enable "KUBERNETES_SERVICE_ACCOUNT_OVERWRITE" 

In order to allow pipelines to overwrite the service account used in the pipelines, we must set a values on the Gitlab Runner being used to create the pods on the Openshift side.

1. Navigate to your Gitlab-Ci's Namespace inside of your Openshift

2. Create a ConfigMap setting `KUBERNETES_SERVICE_ACCOUNT_OVERWRITE_ALLOWED` equal to the service acconuts you would want to allow use of. Below is an example which will allow the use of all service accounts 

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: gitlab-ci
  namespace: gitlab-ci
data:
  KUBERNETES_SERVICE_ACCOUNT_OVERWRITE_ALLOWED: .*
```

3. Connect the Gitlab runner to the newly created ConfigMap
  1. `Installed Operators -> Gitlab Runner -> Gitab Runner(tab)`
  2. Choose the runner you would like to edit and navigate to the `yaml` tab
  3. Update `spec.env` to point to your new ConfigMap `env: gitlab-ci`

```
apiVersion: apps.gitlab.com/v1beta2
kind: Runner
metadata:
  name: gitlab-runner
  namespace: gitlab-ci
spec:
  buildImage: alpine
  env: gitlab-ci
  gitlabUrl: 'https://git.delta.com'
  serviceaccount: my-service-account
  tags: openshift
  token: gitlab-runner-secret
```


  