---
title: "Monotoring"
date: 2025-02-16 00:00:00 +0800
categories: [Microservices]
tags: [microservices]
---

## What should be logged?

Given that logging has to be added explicitly by the developer and is always optional, how do you choose what to log? Here are a few examples:

 - What to log:
  1. Pertinent events in your application and their details
  2. Success/failure of important operations
 - What not to log:
 1. Things that can easily be ascertained from other sources
2. Anything that’s secret or sensitive
3. Any personal details about your users

If you find yourself drowning in details from too much logging, feel free to go in and remove logging that isn’t useful. For every console log, you just have to ask the question, can I live without this detail? If you don’t need it, delete it.

Generally speaking, though, more logs are better than fewer logs. When it comes to debugging in production, you need all the help you can get to understand why a problem occurred. Tracing back through the log is an important step in understanding the sequence of events that resulted in the problem.

You won’t be able to add more logging after the problem has occurred! Well, you can if you isolate and reproduce the problem, but that in itself can be difficult. More logging is better because when you do hit a problem, you want to have as much information as possible to help you solve it.

A common solution for large-scale enterprise monitoring of microservices is the combination of Fluentd, Elasticsearch, and Kibana. Other options specifically for monitoring metrics are Prometheus and Grafana