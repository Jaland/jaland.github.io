---
layout: post
title:  "Tips and Tricks for Openshift 4 UI"
tags: openshift ui
category: openshift
---

# Tips and Tricks for Openshift 4 UI

I am going to use this post as a way to Document little tips and tricks I have learn over the years for the Openshift UI. Note I am going to make a seprate post for using the `oc` cli 



## Debugging Issues

So this section is going to be a little more opinionated. But when I first came to Openshift my #1 problem with a bullet was figuring out what the heck was going on when somethign went wrong. 

I would dig into `pod` logs then I would go and see if something went wrong in the `deployment` events and I would check to make sure the `replicaController` was set up correctly. It took FOREVER to figure out the issue as I went and looked at the fifty different pieces of my application one by one. Honestly if you get nothing else out of my tips and tricks section listen to this.

USE THE `MONITORING` VIEW UNDER DEVELOPER!!!!

The `monitoring` view simply aggregates all of the events for a given project (or all the projects) and streams them out. This will save you so much time.

So let me walk you through how it works very quickly. 

First change to the `Developer` perspective, click `Monitoring`, and change to the `Events` tab


![Openshift 4 - Monitor Logs](/assets/living_post/openshift-ui/monitoring.png)

As you can see with the example above there is something wrong with our `PLR`(`PipeLineRun`). From here we can just click the link to our `PipelineRun` check out the logs and see it does not like the name of the image we are trying to push to quay.io

![Openshift 4 - Monitor Logs](/assets/living_post/openshift-ui/monitoring-logs.png)

And again I know this example seems specific to pipelines, but it does not really matter what the issue is. If it was a `pod` with a crashback loop up that would be obvious. If it is a `volume` that is not mounting properly that would be immediately apparently. When you are trying to figure out why your deployment is not working this Monitoring tool is a godsend.


## Download a CLI tool

You can download the exact CLI tool associated with a specific Openshift release by downloading it directly from your openshift instance.

This can be found by clicking the `?` button in the top left and choosing `Command Line Tool`

![Openshift 4 - CLI](/assets/living_post/openshift-ui/cli-tool.png)
