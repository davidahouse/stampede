---
layout: default
title: Why?
parent: About
nav_order: 1
---

### Why create another automation system?

First of all, I don't think all other systems have some fatal flaws that cause them to be terrible. They are good at what they do and fill the needs of the people that use them pretty well. Many of the hosted solutions are excellent and cover many of the uses that most developers have.

So why create another one? The primary reason is because I wanted to create one for myself to better understand how they work. Seems counter-intuitive, but I usually do things the hard way.

### Ok, well there must have been other reasons?

My current employer Kaiser Permanente has an interesting environment for mobile apps as we have some in the public app stores, and many internal apps hosted by an internal app store. Also we have policies in place that prevent the usage of most publicly hosted solutions. For many years we have run Jenkins servers and recently have built a comprehensive pipeline for our mobile app teams to use.

Overall, however, there are limitations in how Jenkins operates. While powerful in its use of plugins, this also moves control out of the hands of our build engineers and they are at the mercy of how the plugins work. We wanted a system that gave our build engineers complete control and provide a great user experience to developers.

### The foundational principles

Having identified that we wanted a new system, we came up with some foundational principles that we built the system with:

  - Provide the best user experience in GitHub/JIRA that we possibly can. Using Pull Request checks, integration with wiki and releases with the idea that the developer never needs to leave these tools to understand what has executed and the status of it. We should provide rich output for tasks on success or failure.
  - Allow tasks to run in parallel as much as possible.
  - Allow for simple but powerful queuing and node management so that admins can fine tune the system in any scale they desire.
  - Put control of what is executed in the hands of admins and build engineers. Developers only need to provide basic info for what tasks they want to happen, while the details of how they execute are managed by build engineers.
