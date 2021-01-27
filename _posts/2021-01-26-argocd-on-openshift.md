---
layout: post
title:  "ArgoCD on Openshift - HelloWorld"
tags: openshift kubernetes operators helloworld
category: openshift
---

# ArgoCD on Openshift - HelloWorld


## Deploying ArgoCD to the cluster (Operator)

For my first deploy I am just going to use the Red Hat provided Operator on Openshift

Deploy that with `Operator Hub` -> `ArgoCD`
<sub>I used default settings and named my deployment `argocd`</sub>

Inside the Operator create a new `ArgoCD`

Create a passthrough route (change service name as needed):
```
oc create route passthrough argocd --service=argocd-server --port=https --insecure-policy=Redirect
```

Password is stored in cluster level secret `secret/argocd-cluster` -> `admin.password`


## Deploy Application to ArgoCD


### Connect Helm Repo

This is required for my personal release.

1. Click `Manage your repositories, projects, settings` (The gear on the left)

2. Click `Repositories`

3. Click `Connect Repo Using HTTPS`
  * **Type** - helm
  * **Name** - bitnami
  * **Repository URL** - https://charts.bitnami.com/bitnami

### Deploy the application

1. Click `New App`
* **Application Name** - jboss-application
* **Project** - Default
* **Sync Policy** - Manual
* **Sync Options** - Check `Use a schema to validate resource manifest` 
* **Repository URL** - https://github.com/jland-redhat/jboss-application-helm.git
* **Revision** - argocd
* **Cluster URL** - https://kubernetes.default.svc (local cluster)
* **Namespace** - argo-managed-example

2. Click `Create`
<sub>Note That `HELM` section exist now at the bottom, and contains a list of parameters.<sub>

### Sync up our repositories

Now we just want to have argo actually deploy our application.

First we need to let the argo service account have access to the `argo-managed-example` namespace, for this demo we are just going to give it admin:

```
 oc adm policy add-cluster-role-to-user cluster-admin -z argocd-application-controller
```

Now we just need to sync up by clicking the `sync` button.\


## Notes and thoughts based on my first experience

Pretty impressed at how easy it was to get argoCD to get set up. Also neat how the UI view is able to both identify all the resources and show how they are connected. I.E. It shows the `DeploymentConfig` created a `ReplicationController` that created the `Pod`

### Follow up questions

So it seems like the `Pipeline` and `DeploymentConfig` keep falling out of sync.

Not sure why the pipeline would go out of sync, but the DC I assume is because we are deploying to it directly from the pipeline. Probably need to do more research on the best way to manage this.