---
layout: post
title:  "Tips and Tricks for Tekton"
tags: tekton openshift pipelines cicd
category: tekton
---

# Intro

At this point I have had some time with to work Tekton (Specifically the "Openshift Pipelines" flavor). Having some experience with Jenkins a similar tool I can say that Tekton has some major advantages and disadvantages. 

On the positive side. Since Tekton is built into the control plane, and is defined through CRD(Custom Resource Definitions), I personally find it easier to understand how to use from a Kubernetes perspective. It also makes it makes for a better IaC experience than a Jenkins file.

On the other side, Tekton does still feel like a fairly immature technology as of the writing of this article. There are a lot of quality of life items that it feels like it is missing. For one there seems to be no good way to continue running a pipeline that failed half way through, you just have to restart it. Also one feature that Jenkins has is the ability to mark a pipeline as "unstable" if certain conditions are met where you don't want to fail the pipeline. There are a hand full of other items that feel like they have not been fully flushed out but hopefully that will be fleshed out over time. 

# General Notes

TBD