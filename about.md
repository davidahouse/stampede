---
layout: category
title: About
permalink: /about/
has_children: true
---

Stampede is an option source continuous automation system similar to [Jenkins](https://jenkins.io). With Stampede you can setup a complete set of integrations for your code repositories, or use it as a general purpose automation platform.

## Why create another automation system?

First of all, I don't think all other systems have some fatal flaws that cause them to be terrible. They are good at what they do and fill the needs of the people that use them pretty well. Many of the hosted solutions are excellent and cover many of the uses that most developers have.

Stampede was created for use by teams that want rich PR feedback in GitHub, want total control over what commands are executed on their build servers and are unable to use cloud based offerings. If you don't need these things, you should look at the great alternatives out there. But if you do want these things, read on!

## The foundational principles

Having identified that we wanted a new system, we came up with some foundational principles that we built the system with:

- Provide the best user experience in GitHub that we possibly can. That means Stampede should run as a GitHub app and should be able to provide rich feedback on Pull Requests. It should also be able to trigger automations from any of the GitHub app events that can be triggered (pushing to a branch or creating a release for example). And when the automation tasks complete, they should provide details on why they failed or were successful.
- Allow tasks to run in parallel as much as possible.
- Allow for simple but powerful queuing and node management so that admins can fine tune the system in any scale they desire.
- Put control of what is executed in the hands of admins and build engineers. Developers only need to provide basic info for what tasks they want to happen, while the details of how they execute are managed by build engineers.
- Provide a good UI for finding the list of builds executed and their status, along with reporting to understand the usage of the system and prepare for the future.

## Prerequisites

Stampede is built using `node.js` and will need to run on a system that can support at least `Node 8`. To operate, Stampede will need an instance of `postgres` and `redis`. And of course it will need the ability to send the events from GitHub to Stampede via a web hook. Connecting GitHub to Stampede can be done with a web proxy, or directly if your Stampede instance is visible to GitHub.

To get started, checkout our [Getting Started Guide](/gettingstarted).
