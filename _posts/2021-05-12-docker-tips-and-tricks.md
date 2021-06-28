---
layout: post
title:  "Tips and Tricks Docker"
tags: docker
category: docker
---


# Build a DockerImage

```docker build -t <IMAGE_NAME> .```

# Start and enter a DockerImage

```docker run -it --entrypoint /bin/bash <IMAGE_NAME>```