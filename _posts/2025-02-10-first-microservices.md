---
title: "First microservices"
date: 2025-02-10 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---

[GitHub Repository](https://github.com/bootstrapping-microservices-2nd-edition/chapter-2)

Node.js is network orientated and high performance, making it well suited to building microservices.


Node.js is open source, and you can find the code for it on GitHub at [https://github.com/nodejs/node](https://github.com/nodejs/node).

 Npm is the Node Package Manager. It’s a command-line application that talks to the npm repository online and allows you to manage third-party packages in your Node.js project. Installing a readily available package is a fast way to solve a problem you’d otherwise have to write more code to achieve! You can search for packages on the npm website at [www.npmjs.com](https://www.npmjs.com)

We might extract the code to a separate Node.js project to isolate the problem and make it easier to solve
[https://www.data-forge-notebook.com/](https://www.data-forge-notebook.com/)


Running multiple versions of Node.js is easy. Say you’re maintaining or have to work on multiple production applications that are built with different versions of Node.js. Or, maybe you’re just working on a single application, but it has been in development for quite some time, and different microservices are on different versions of Node.js. In these cases, I highly recommend you use nvm (the Node Version Manager) to install different versions of Node.js and switch between them.

There are actually two different applications called nvm, and which one you choose depends on your operating system. See the following links for setup instructions:

For Linux and macOS: https://github.com/nvm-sh/nvm
For Windows: https://github.com/coreybutler/nvm-windows
This isn’t for the faint of heart! You must be proficient at using the command line to install this software.

Create an empty Node.js project
```ssh
create an empty Node.js project
```

The -y argument means that we don’t have to answer any interactive questions while initializing our project. That makes it a little simpler and faster to create our project.

```
**Package.json vs. package-lock.json**

Although package.json is automatically generated and updated by npm, it can also be edited by hand. That way, you can manually change the metadata and npm module dependencies for your Node.js project.

Usually, package.json doesn’t specify exact version numbers for dependencies (although it can if you want it to). Instead, package.json generally sets the minimum version for each dependency and can also set a range of versions. In addition, package.json only tracks top-level dependencies for the project. You don’t need to specify dependencies of dependencies; that’s handled automatically for you. This makes package.json smaller, more concise, and therefore more human readable.

The problem with package. is that you and your colleagues can end up running different versions of dependencies. Even worse, you could be running different versions compared to what’s in production.

That’s because package.json usually doesn’t specify exact versions, so depending on when you invoke npm install, you can get a set of versions different from everyone else. This is a recipe for chaos and makes it difficult to replicate production problems because you may not be able to reproduce the exact configuration that is running in production.

Package-lock.json was introduced in npm version 5 to solve this problem. It’s a generated file and isn’t designed to be hand edited. Its purpose is to track the entire tree of dependencies (including dependencies of dependencies) and the exact version of each dependency.

You should commit package-lock.json to your code repository. Sharing this file with teammates and the production environment is the best way to ensure that everyone has the same configuration for their copy of the project.
```
Express is the most popular code library for building HTTP servers on Node.js. You can find documentation and examples on the Express website at http://expressjs.com/.

Install the latest version of Express
```sh
npm install --save express@[version]

npm install --save express@5.0.0-beta.1
```

 To get our microservice ready to run in production

 ```sh
npm install --omit=dev
 ```
 --omit=dev to omit development dependencies and only install those dependencies that are required in production

 we’ll start running our microservice using the following standard convention for Node.js

 ```
npm start
 ```
We use nodemon to run our microservice, and it automatically watches for code changes in our project. When a code change is detected, nodemon automatically restarts our microservice for us, saving us the effort of doing so manually.

```sh
npm install --save-dev nodemon
```

Note that now we’re using the --save-dev argument. This installs the package as a dev dependency rather than a production dependency

Now that we’re going to be using nodemon instead, we’ll replace node with nodemon and run it like this:

```sh
npx nodemon src/index.js
```

The npx command that’s suddenly appeared is a useful command that comes with Node.js and allows us to run locally installed packages directly from the command line.

Now that we’ve defined the start:dev command, we can run our microservice in development mode like this:

```sh
npm run start:dev
```

Notice the use of npm run to run our new script. We can use npm run to run any npm script that we add to our package.json file. This is a great way to add build scripts and other utility scripts to our project. We can omit the run part for npm start and npm test

Now, install the dependencies:
```sh
npm install # install the dependencies
npm install --omit=dev # If you wanted to simulate a production deployment
npm start #To run it like you would in production
npm run start:dev #To run it with live reload for fast development
```





