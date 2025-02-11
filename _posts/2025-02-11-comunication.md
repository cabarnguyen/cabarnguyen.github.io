---
title: "Communication between microservices"
date: 2025-02-11 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---

[GitHub Repository](https://github.com/bootstrapping-microservices-2nd-edition/chapter-5)

 RabbitMQ software for queuing messages, which will help us decouple our microservices. We’ll use the npm package called amqplib to connect our microservices to RabbitMQ so they can send and receive messages

 With the start:dev npm script in place, we can run our microservice like this:

 ```sh
npm run start:dev
 ```
This invokes nodemon for our microservice like this:

```sh
nodemon --legacy-watch ./src/index.js. 
```

 I used the --legacy-watch argument with nodemon. I used this argument because I usually run Docker and Docker Compose under WSL2 on Windows. The --legacy-watch argument disables the filesystem watch and, instead, uses a frequent polling mechanism to monitor for code changes. If you do your development under WSL2 or on a virtual machine (VM), you need this because the automatic file watch required by live reload doesn’t translate changes through from the host operating system. If you’re not doing your development under WSL2 or a VM, you can safely remove the --legacy-watch argument, and your live reload will work with slightly better performance.

We should use indirect messages when the sending microservice doesn’t care if any subsequent action is taken or not. We can also use it for broadcast-style notifications to the entire application (e.g., a notification of an important event that other microservices would like to know about).

A key question with microservices communication is how do we direct a message to another microservice? The simplest answer to this question is to use the ubiquitous DNS, which translates hostnames to IP addresses. This works automatically with Docker Compose (the container name is the hostname) and also doesn’t require much effort to have it work within our production Kubernetes cluster.

Using Docker and Docker Compose for development as we’ve been doing means that DNS works automatically, and we can rely on it. When we deploy to our production Kubernetes cluster, we’ll have some more work to do to make our microservices accessible via DNS

If you get any errors about containers already created, it might be because you left the previous example running. When moving on from each example, be sure to shut it down using

```sh
docker compose down
```

Direct messaging can be useful to coordinate behaviors in an explicit way or well-defined order.

RabbitMQ allows us to decouple message senders from message receivers. A sender doesn’t know which, if any, other microservices will handle a message.

RabbitMQ is well-known and established software for queuing messages. Many companies commonly use RabbitMQ, and it’s my go-to solution for indirect messaging. Developed over a decade ago, RabbitMQ is stable and mature. Among other protocols, it implements the Advanced Message Queueing Protocol (AMQP), which is an open standard for message-broker communication.

The message sender uses DNS to resolve the IP address of the RabbitMQ server. The message sender then communicates with RabbitMQ to publish a message on a particular named queue or exchange. The receiver also uses DNS to locate and communicate with the RabbitMQ server to retrieve the message from the queue. At no point do the sender and receiver communicate directly.

Port 5672 is the port number we’ll soon use with amqplib to send and receive messages through RabbitMQ. The other port is 15672, which we’ll use to access the RabbitMQ management dashboard.

Install the amqplib npm package into each microservice that needs to connect to RabbitMQ:

```sh
npm install --save amqplib
```

Delays starting our microservice until the RabbitMQ server is ready. We’ll use the handy wait-port command installed using npm

```sh
npm install --save wait-portFROM node:18.17.1
 
WORKDIR /usr/src/app
COPY package*.json ./
 
CMD npm install --prefer-offline && \
  npx wait-port rabbit:5672 && \
  npm run start:dev

```

Single-recipient messages are one-to-one : a message is sent from one microservice and received by only one other microservice. This is a great way of making sure that a particular job is done only once within your application.

Multiple-recipient messages are one-to-many : a message is sent from only a single microservice but potentially received by many others. This is a great way of publishing notifications within your application.

![Table 5-2](https://drek4537l1klr.cloudfront.net/davis6/HighResolutionFigures/table_5-2.png)