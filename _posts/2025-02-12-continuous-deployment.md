---
title: "Continuous deployment"
date: 2025-02-12 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---

[GitHub Repository](https://github.com/bootstrapping-microservices-2nd-edition/chapter-8-example-1.git)

 In many respects, CI is a stepping stone to CD, and usually setting up a CI pipeline is simpler than setting up a CD pipeline because CD pipelines have basically grown out of CI pipelines. Indeed, a CD pipeline is often the same as a CI pipeline, but with the additional feature that it also does deployment

 But what exactly is a CI pipeline? A CI pipeline is an automated process that detects changes in our codebase and runs various checks and balances against the code to make sure that it still functions. Often, a CI pipeline includes building the code and running a linter against it, but usually the most important purpose of most CI pipelines is to run automated tests against our code.

We can think of a CI pipeline as an early warning system for problems in our codebase.

CD is a technique where we do frequent automated deployments of our code to a production (or testing) environment. Essentially, making updates to our code automatically triggers new deployments of our software. A CD pipeline builds on a CI pipeline: it’s very similar but adds extra steps to deploy our code to production.

The terms continuous delivery and continuous deployment have both been used in our industry. These terms sound similar and are very similar in their meaning. It can be quite confusing because they both abbreviate to CD and are very close in meaning, making it difficult to remember which is which (even for me). Hopefully this makes it simple:

 - Continuous delivery means we’re always ready to deploy our software to production, but deployment can be automated or manual.

 - Continuous deployment means that every change we make to our software is automatically deployed to production (assuming it passes tests and other checkpoints).


 [Example-2](https://github.com/bootstrapping-microservices-2nd-edition/chapter-8-example-2)

 [Example-3](https://github.com/bootstrapping-microservices-2nd-edition/
 chapter-8-example-3)

 Set the following environment variables (in Windows, open a Linux terminal under WSL2):

 ```sh
export CONTAINER_REGISTRY=<url-to-your-container-registry>
export VERSION=1
 ```

 If permissions should not already be set correctly

 ```sh
chmod +x  filepath-sh
 ```

 You can learn more about setting environment variables in your workflow at [store-information-in-variables](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/store-information-in-variables)