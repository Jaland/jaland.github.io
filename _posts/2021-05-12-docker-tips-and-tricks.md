---
layout: post
title:  "Tips and Tricks Docker"
tags: docker
categories: docker
---


# Build a DockerImage

```docker build -t <IMAGE_NAME> .```

# Start and enter a DockerImage

```docker run -it --entrypoint /bin/bash <IMAGE_NAME>```

# Build Container Image and expose ports

```sh
podman build . -t my-container
podman run -p 8080:8080 -p 9000:9000 localhost/my-container:latest
```