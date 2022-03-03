---
layout: post
title:  "Setting up an ArgoCD instance on Openshift"
tags: argocd gitops openshift49
category: gitops
---


# Introduction

This is a basic instruction set for creating an ArgoCD instance on Openshift 4.9.

> Important:
    This assumes that the operator has already be successfully installed

# Install ArgoCD Instance

## Overview

First lets create a new new namespace called `<PREFIX>-gitops` where prefix will be something indicating it is an Argo instance used by your group. Or if it is to be used to control the entire cluster (or multiple clusters) I would recommend using the name `developer-gitops`  

One thing to note is that Openshift is going to have its own namespace `openshift-gitops` with its own instance of argo. It is valid to use this as your primary Argo instance if desired, and this may make sense depending on the size of your cluster. But creating a new instance will help keep your Application infrastructure separate from your Cluster's infrastructure and make for a cleaner experience.

> Important:
    The CRDs related to Argo (I.E. Applications, Projects, ApplicationSets, etc..) will have to be installed on the same namespace that your Argo instance lives on. This means your developers will need at to be given access to Get/Create ArgoCd `Application` CRDs

To complete the actual installation of ArgoCd navigate to the `Installed Operators` on the admin view, Find the `Red Hat OpenShift GitOps` operator (again this lab assumes you already have that operator installed ClusterWide), open the `Argo CD` tab, and click `Create ArgoCD`.

## Install

In order to create your Argo instance navigate to `Installed Operators -> Red Hat Openshift Gitops -> ArgoCD` and click the `Create ArgoCD` button. From here you can fill out the fields as you see fit. For the demo I simply created the Argo using the defaults.

![Argo Setup](/assets/living_post/argo-setup/install-argo-cd.png)

## Validate

Installation of your ArgoCD Instance can be validated by navigating to `Workloads -> Deployments` inside of the Admin view. After a few minutes you should see all of your Argo related deployments (`argocd-dex-server`, `argocd-redis`, `argocd-repo-server`, `argocd-server`) are up and running.

Navigate to `Networking -> Routes` in order to get the url for your newly created ArgoCD Instance, and you should be able to `Login using Openshift`

# Setup ArgoCD

## Setup RBAC

ArgoCD has its own RBAC system controlled by the ConfigMap `argocd-rbac-cm`, more information for setting that up correctly can be found [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/rbac/)

For our demo purposes we are simply going to set the default role to admin, allowing all users who can login to the cluster the ability to use argo to deploy resources.

* Navigate to `Installed Operators -> Red Hat Openshift GitOps`
* `Argo CD` tab, select your instance of `ArgoCD`
* Click the YAML tab
* Update `ArgoCD.spec.rbac.defaultPolicy` to `role:admin`

## Setup Argo Project

ArgoCD has a concept of [Projects](https://argo-cd.readthedocs.io/en/stable/user-guide/projects/) which is a way to organize your `Application`s. While it is possible to just put everything under the provided `default` project it will make the UI much cleaner if your apps are organized into projects.

To Create A new Project:

* Navigate to `Settings` (the gear on the left hand side of the screen)
* Click `Projects`
* Click `New Project`
* Give your project a name and description

Next we are going to need to allow `Application`s in or project to pull from our repository. To do this:

* Click the `edit` button inside `Source Repositories`
* Click `Add Source`
* Enter the url of your git repo (Note that wildcards are allowed)

Finally we are going to need to allow Argo to deploy to our projects Namespace. Which will require two steps

First we need to allow our Destination on the ArgoUI which can be done by:

* Clicking `Edit` on the `Destinations` section of our `Project`
* Filling out the fields (for server use `https://kubernetes.default.svc` in order to deploy onto the same server that Argo is deployed on)

## Give Argo Access to our Dev Environment

Now we are going to need to do is to give permissions to our Argo instance actually deploy Kubernetes resources onto each of our destination namespaces. This requires 2 steps.

First we need to update Argo's cluster setting to include our namespace:

* Navigate to `Settings`
* Click `Clusters`
* Click the `In-Cluster` option
* `Edit`
* Add `myapp-dev` to the `Namespaces` list

> Tip
  If you are seeing the error `Namespace ... for Resource ... is not managed` you are probably missing the above step

Next we need to allow the ArgoCD controller service account to actually have access to modify resources on our application namespace. This can be done with the following command:

``` shell
oc policy add-role-to-user \
   edit \
   system:serviceaccount:<ARGO_CD_NAMESPACE>:argocd-argocd-application-controller \
   --rolebinding-name=argocd-edit \
   -n <APPLICATION_NAMESPACE>

```

> Important:
    In theory giving the argocd controller service account edit access should be enough, but I had to modify this to a custom role that basically gave complete access following the suggestion found [here](https://access.redhat.com/solutions/6012601)

I installed a role on my Application's namespace looked like:

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: argocd-access-role
  namespace: <APPLICATION_NAMESPACE>
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
```

Then link to Argo's service account with:

``` shell
oc policy add-role-to-user \
   argocd-access-role \
   system:serviceaccount:<ARGO_CD_NAMESPACE>:argocd-argocd-application-controller \
   --rolebinding-name=argocd-super-edit \
   -n <APPLICATION_NAMESPACE> --role-namespace=<APPLICATION_NAMESPACE> 

```

> Tip
  If you are seeing the error `forbidden: User "system:serviceaccount:developer-gitops:argocd-argocd-application-controller" cannot list resource ... in namespace` you are probably missing the above step

# Test it out

Congrats you are all done setting up your ArgoCD, and should be able to navigate back to the main UI and create a new deploying using the `+ New App` button.
