---
layout: post
title:  "AWS Tips and Tricks"
tags: AWS
categories: aws cli ec2
---

# Intro

Doing an install of ARO Red Hat Openshift using Terraform and wanted to capture some of the items I am learning navigating Azure. It is similar but pretty different than AWS.

## Change Subscriptions

If you have multiple subscriptions in the same account you can change your subscription with:

```sh
az account set --subscription <name or id>
```

And you can see your current subscription with:

```sh
az account show
```

## DNS

If your service has a public IP address you can update your DNS pretty quickly by choosing using the `Azure Resource` alias type and choosing Azure resource

![](assets/living_post/azure-tips/dns-a-record.png)

## Service Principle

Finding SPs seem to be a pain in the butt! Best way I found is if you know the ID of the SP then you can search for it and find the specific object.

## ARO

After a fresh install this command can be used to retrieve the `kubeadmin` password

```sh
AROPASS=$(az aro list-credentials --name $AZR_CLUSTER --resource-group $AZR_RESOURCE_GROUP -o tsv --query kubeadminPassword)
echo $AROPASS
```