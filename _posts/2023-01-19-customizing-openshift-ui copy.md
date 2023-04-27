---
layout: post
title:  "Customizing Openshift Menu"
tags: openshift consolelink
category: Openshift
---

# Customize Openshift Menu

Customization of the Openshift Menu can be done using console links. It is pretty simple and can really make the instance of Openshift feel more branded to the organization you are a part of

```yaml
apiVersion: console.openshift.io/v1
kind: ConsoleLink
metadata:
  name: namespaced-dashboard-link-for-all-namespaces
spec:
  href: 'https://www.example.com'
  location: NamespaceDashboard
  text: This appears in all namespaces
  ```