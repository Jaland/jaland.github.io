---
layout: post
title:  "Tekton General Thoughts and Architecture"
tags: tekton openshift pipelines cicd
categories: tekton
---

# Intro

At this point I have had some time with to work Tekton (Specifically the "Openshift Pipelines" flavor). Having some experience with Jenkins a similar tool I can say that Tekton has some major advantages and disadvantages. 

On the positive side. Since Tekton is built into the control plane, and is defined through CRD(Custom Resource Definitions), I personally find it easier to understand how to use from a Kubernetes perspective. It also makes it makes for a better IaC experience than a Jenkins file.

On the other side, Tekton does still feel like a fairly immature technology as of the writing of this article. There are a lot of quality of life items that it feels like it is missing. For one there seems to be no good way to continue running a pipeline that failed half way through, you just have to restart it. Also one feature that Jenkins has is the ability to mark a pipeline as "unstable" if certain conditions are met where you don't want to fail the pipeline. There are a hand full of other items that feel like they have not been fully flushed out but hopefully that will be fleshed out over time. 

# Architecture

Firstly, since Tekton is made up completely of Kubernetes resources I have found the best way to store the pipelines in an IaC manor is through a Helm chart, which can than be installed either directly or through ArgoCD/Gitops.

# How to handle `Task`

When creating a basic Tekton pipeline such as [here](https://github.com/Jaland/pipeline-chart) it is simple enough to just package everything (`pipeline`,`task`, etc) together. But if you are attempting to design for reusability it may make more sense to have a set of "common" task either at the namespace or cluster level that can be reused by multiple pipelines. Note that the idea of inheritance may seem like a good idea here (I.E. having the Helm chart pull in these task using a subchart) this will cause issues as soon as you attempt to pull in the same set of Task in a second chart due to the Task already existing in the original chart.


Instead I have found the best way to handle common task is having a chart that contains just your Task kubernetes object and then a separate chart containing each pipeline you are attempting to run. As far as how to deal with secrets (especially with things like vault that may require annotations at the Task level), I am still unsure of the best way to handle. 
