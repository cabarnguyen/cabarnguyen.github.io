---
title: "Publish first microservice"
date: 2025-02-10 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---

[GitHub Repository](https://github.com/bootstrapping-microservices-2nd-edition/chapter-3)


Docker works on Linux, MacOS, and Windows 10. If you are working on Windows, you’ll first need to install WSL2 (the Windows integrated Linux kernel)

```sh
docker build -t video-streaming --file Dockerfile .
```
The -t argument allows us to tag or name our image

Here’s the general format for the docker build command:

```sh
docker build -t <your-name-for-the-image> --file <path-to-your-Dockerfile> <path-to-project>
```

```sh
docker run -d -p 3000:3000 -e PORT=3000 video-streaming
```
The -d argument causes our container to run in detached mode

The -e argument sets the PORT environment variable to 3000

Here is the general format for the docker run command:
```sh
docker run -d p <host-port>:<container-port> -e <name>=<value> <image-name>
```

```sh
docker logs <container-id>
```

Debuging the container
```sh
docker exec -it <container-id> bash
```
The docker tag command has the following general format

```sh
docker tag <existing-image> <registry-url>/<image-name>:<version>
```

Here is the general format for docker push

```sh
docker push <registry-url>/<image-name>:<version>
```