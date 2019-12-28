---
layout: default
title: System components
parent: About
nav_order: 2
---

Stampede is a multi-component system that has only a couple of required components to function, and additional components that provide additional functionality.

## Required components

### Node

The system requires at least Node v8. It can run on any system that node can run on.

### Redis

Redis is the core of the system to provide a cache for the components to use while operating, and the queues that allow components to communicate with each other.

### Stampede server

The `stampede-server` app provides the integration with GitHub to receive events that trigger automations to happen. Each stampede instance should have a single `stampede-server` app running.

### Stampede worker

At least one `stampede-worker` app should be running as well. The workers are the components that execute the automation tasks, so at least one worker has to be available for running tasks. The `stampede-worker` and `stampede-server` communicate with each other via Redis queues. Many worker instances can be configured to provide as much scale as needed. While each worker can only read from a single queue, you can configure many workers to service the same queue. And you can also configure as many queues as needed to provide a finely tuned system as required.

### Stampede CLI

The `stampede-cli` is a command line application used to configure parts of the system. Admins and build engineers can use the cli to add/configure the tasks that the system uses. It can also be used to monitor the current activity happening on the server.

## Optional components

### Postgres

If you are using the `stampede-portal` component (detailed below), you will need to configure a postgres instance. Details of the builds and tasks that are executing on the stampede system are stored in the database and are used for troubleshooting and reporting on activity on the system.

### Stampede portal

The `stampede-portal` app can be instantiated to provide a web interface and activity storage into the postgres database. With the portal, admins can configure the system, monitor the current activity and look at the historical details of build and task execution. The portal receives notifications from the `stampede-server` and will store the details into the postgres database. The portal is also responsible for hosting the REST API that the mobile app uses.

### Stampede mobile app

A mobile app is available that provides many of the same functions as the portal, just in a native mobile app instead. It requires an instance of the `stampede-portal` available as that is where the REST API is hosted. The mobile app also has more detailed live views of the currently executing tasks and worker details that can be used to get quick at-a-glance status of the system.
