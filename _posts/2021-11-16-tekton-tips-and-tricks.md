---
layout: post
title:  "Tips and Tricks for Tekton"
tags: tekton openshift pipelines cicd
category: tekton
---

# Intro

I am going to use this as a notepad for some different tips and tricks I have learned while using tekton


## Useful Functions

| Function | How To Use | Example | Extra Tips |
| -------- | ---------- | ------- |----------- |
| `required` | This function can be used to force a user to fill in a value, and what I find really makes it one of my favorite functions is the fact you can give a return message which is very obvious straight forward feedback. | `{{ .Value.requiredValue \| required ".Value.requiredValue is required, can be included through the cli using --set or by adding the value to your values.yaml file"}}` |