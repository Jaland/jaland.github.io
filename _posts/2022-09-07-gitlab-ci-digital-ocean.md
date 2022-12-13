---
layout: post
title:  "Using Github CI to Deploy to Digital Ocean"
tags: github-ci github ci digital-ocean
category: ci
---


# Introduction

I know it has been a bit since my last post but I have been working on a fairly large project learning Godot that I am hoping to write about in the near future (but also I am going on a 3 week honeymoon in a week so not that soon).

But as part of my new learning I want to create a GitHub CI Pipeline for deploying an image to the [DigitalOcean](https://cloud.digitalocean.com/) repository I have created!

# Creating your Pipeline

## Creating the Image Repository

Creating an image repository on [DigitalOcean](https://cloud.digitalocean.com/) is fairly simple. Navigate to the `Container Registry` option on the left hand menu, click `Create New` and follow the instructions. Since I only plan to store one image for now I just went with the free "Starter" option, but you have the option to upgrade later.

