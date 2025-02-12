---
title: "The road to production"
date: 2025-02-11 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---


[GitHub Repository](https://github.com/bootstrapping-microservices-2nd-edition/chapter-6)

Kubernetes is a computing platform for managing container-based applications. It was originally created by Google but is now managed by the Cloud Native Computing Foundation (CNCF), a committee that has huge industry support and is also responsible for many other interesting projects.

A Kubernetes cluster is normally composed of multiple computers called nodes. For our purposes, each node will be a virtual machine (VM), although you can also run Kubernetes on physical hardware as well if you do all the setup work yourself. We can add as many nodes as we need to our cluster to expand the amount of computing power available to our application. Each node can host multiple pods. (A pod is the basic unit of computation in Kubernetes.). Each pod can host multiple containers.

It’s the Kubernetes deployment that will continuously monitor the pod, and if the microservice crashes or hangs, then the deployment will detect that and automatically start a new instance of the microservice.

To learn more about Kubernetes running under Docker Desktop, see their documentation at https://docs.docker.com/desktop/kubernetes/.

Build image to production

```sh
docker build -t video-streaming:1 --file Dockerfile-prod .
```

Deploying our microservice to Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: video-streaming
spec:
  replicas: 1
  selector:
    matchLabels:
      app: video-streaming
  template:
    metadata:
      labels:
        app: video-streaming
    spec:
      containers: 
      - name: video-streaming
        image: video-streaming:1
        imagePullPolicy: Never
        env:
        - name: PORT
          value: "4000"
---
apiVersion: v1
kind: Service
metadata:
  name: video-streaming
spec:
  selector:
    app: video-streaming
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 4000
      nodePort: 30000

```

The YAML configuration in listing is broken up into two main sections that are separated by three hyphens (- - -). The first section creates the Kubernetes deployment that keeps our microservice alive (automatically restarting it when it crashes).

The second section creates the Kubernetes service that exposes our microservice to HTTP requests via DNS. Having these two sections in the same file is optional. If we wanted, we could create a different structure, for instance, with each section in its own file. But it’s convenient to group these together like this because they are the complete configuration for one single microservice.

Kubernetes allows us to choose a port in the range from 30000 to 32767, and it will pick a port at random if we don’t specify a particular one

You can read more about NodePort configuration in the Kubernetes documentation at [Node port](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport).

Check which cluster you’re connected to

```sh
kubectl config current-context
```

If you’ve used kubectl before and connected to other clusters ,  you can connect to your local cluster using the following command:

```sh
kubectl config use-context docker-desktop
```

To see the list of connection contexts
```sh
kubectl config get-contexts
```
Apply deploy

```sh
kubectl apply -f scripts/deploy.yaml
```

See how the name of the pod starts with video-streaming- but ends with a unique number? Kubernetes has generated a unique number for this pod because it could be one of a number of replicas that have been created for this deployment. In this case, we’re keeping things simple, and our configuration file (refer to listing 6.1) only creates a single replica, so we should only see a single pod in this list of pods.

To check the deployments that are now running, invoke the following:

```sh
kubectl get deployments
```

Deploy to AZ
```yaml
#
# To deploy:
#
# kubectl apply -f scripts/deploy.yaml
#
#
apiVersion: apps/v1
kind: Deployment
metadata:
  name: video-streaming
spec:
  replicas: 1
  selector:
    matchLabels:
      app: video-streaming
  template:
    metadata:
      labels:
        app: video-streaming
    spec:
      containers: 
      - name: video-streaming
        image: bmdk1.azurecr.io/video-streaming:1
        imagePullPolicy: IfNotPresent
        env:
        - name: PORT
          value: "4000"
---
apiVersion: v1
kind: Service
metadata:
  name: video-streaming
spec:
  selector:
    app: video-streaming
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 4000
```