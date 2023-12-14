---
layout: post
title:  "Advance Cluster Management (ACM) Tips and Tricks"
tags: acm
category: acm openshift
---

# Advance Cluster Management (ACM) Tips and Tricks

Advance Cluster Management is something I recently used recently and there are a couple things I want to make sure I don't forget

## Custom Policies

The Policy below was very generously created for me by another Red Hatter and does a good job of showing how to do a custom policy that lets us do pretty much anything we would want. Going to break it down a little more below:

``` yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: validate-pipeline-sa
spec:
  remediationAction: inform
  severity: low
  object-templates-raw: |
    - complianceType: mustnothave
      objectDefinition:
        kind: ConfigMap
        apiVersion: v1
        metadata:
          name: fake-policy-holder
          namespace: default
        data:
          fake: holder so policy is not generated empty
    {{- /*  */ -}}

    {{- /* get all pods so we can check which serviceAccount is being used */ -}}
    {{- range $pod := (lookup "v1" "Pod" "" "").items }}
      {{- /* for each pod we need to see if it is using the pipeline sa */ -}}
      {{- /* and the label must match tekton identifier */ -}}
      {{- if and (eq $pod.spec.serviceAccountName "pipeline") (ne $pod.metadata.lables.key "goodvalue") }}
    - complianceType: mustnothave
      objectDefinition:
        kind: Pod
        apiVersion: v1
        metadata:
          name: {{ $pod.metadata.name }}
          namespace: {{ $pod.metadata.namespace }}
      {{- end }}
    {{- end }}
```

So it uses the [Go Template Language](https://pkg.go.dev/text/template) so most of the functions you can use can be found there. (Stuff like range, if/else, etc...)

But the real key item here is this:

``` yaml
{{- range $pod := (lookup "v1" "Pod" "" "").items }}
```

The lookup command is what allows us to look up specific objects to figure out if they exist or not. In this case we want to see if `$pod.spec.serviceAccountName` is `pipeline`. As well as if a labels key does not equal a specific value.
