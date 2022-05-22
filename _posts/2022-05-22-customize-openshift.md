---
layout: post
title:  "Customizing Openshift"
tags: openshift49
category: openshift
---


# Introduction

I recently found out that you can pretty easily customize the menu bar of Openshift allowing you to add commonly used links to your cluster. This means that you can add items like shared AgroCD instances there or even links to external resources like for instance the underlying cloud provider your cluster my be built on.

# How To

## Custom Links

In order to create a custom link to your top App Menu bar just add the following CDR to your cluster:

``` yaml
apiVersion: console.openshift.io/v1
kind: ConsoleLink
metadata:
  name: namespaced-dashboard-link-for-all-namespaces
spec:
  href: 'https://www.example.com'
  location: ApplicationMenu
  text: This appears in all namespaces
  applicationMenu:
    section: Developer Tools
    imageURL: https://via.placeholder.com/24
```

<sub>You can also change the location to `NamespaceDashboard` which puts it on the project info page, but I find that to be less useful</sub>

## Custom Logo

Further customization can be done by adding a custom logo using the following command

``` sh
oc create configmap console-custom-logo --from-file /path/to/console-custom-logo.png -n openshift-config
```

# More Resources

More information about customization can be found on the official documentation [here](https://docs.openshift.com/container-platform/4.9/web_console/customizing-the-web-console.html)
