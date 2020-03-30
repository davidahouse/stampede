---
layout: default
title: Required components
parent: Getting started
nav_order: 1
---

## Dependencies

Before we can get a basic `stampede` system setup, there are several dependencies that we need to make available.

### Node.js

There are many ways to install `node.js`, so go to the official site: [nodejs.org](https://nodejs.org/en/) and install for the platform you want to run `stampede` on. You will need at least version 8 installed, but newer versions should work as well.

### Redis

An instance of `redis` needs to be running for the `stampede` components to use. You can run `redis` in many different ways, but 2 common methods are to use `docker`, or to use a cloud based redis such as [redislabs.com](https://redislabs.com). Once you have an instance of `redis` running, you will need to know the IP address (or name) of the host, and the port that `redis` is running on (the default port is 6379). Optionally you might need a password, depending on the authentication method used to run the server.

## Installing Stampede Server

To install the server component, use the following command:

```
npm install -g stampede-server
```

To configure the server, you can do this by creating a `.stampederc` file in your user home directory. At
the least, the config file should contain the redis connection information you collected above:

```
redisHost=<host name>
redisPort=<port>
redisPassword=<optional password>
```

## Configuring GitHub connection to the server

Connecting the server to GitHub requires creating an `app` in GitHub, obtaining an app id and downloading a PEM certificate. Configuring these items are also done in the `.stampederc` file:

```
githubAppID=<id>
githubAppPEMPath=<path to pem file>
```

Finally the GitHub app needs to have its Webhook URL configured to connect to the server. If you are running your server on the internet, you can connect directly to the server using the default port of 7766. You can change the port using the `webPort=<port>` config.

## Running the server

You can run the server just by executing the `stampede-server` command directly. Another option is to use `pm2` to manage the server. You can install `pm2` by using the command: `npm install -g pm2`. Then starting the server in `pm2` is as simple as: `pm2 start stampede-server`.

## Configuring your first task

Configuring tasks is done by creating a YAML file that represents the details for the task, then using the `stampede-cli` app to upload the details into the redis cache. At a minimum, you will need the following details: a task identifier, a title, the queue that will be used to request execution, the list of parameters and the command for the worker to use to execute the task. A sample task config file would look like this:

```
id: first-task
title: The first task
queue: tasks
config:
  - key: task-parameter
worker:
  taskCommand: task-script.sh
```

This config will enable a task with an id `first-task` with a single parameter `task-parameter` and when it is executed by the worker, the worker will execute the `task-script.sh` command to perform the work.

To load this task into the system, we can use the `stampede-cli` command line admin app. Install it using `npm install -g stampede-cli`. If you have configured the `stampede-server` using a `.stampederc` file in your home folder, you don't need to do anything else to configure `stampede-cli`. Just execute `stampede-cli` on the command line and you will be loaded into the cli. At the `stampede>` prompt, you can type `help` to see the list of commands that the cli supports. For loading the task, we will use the `setTask` command. It simply takes in a path to the YAML file you created above. Once you have loaded the task you can verify it with the `tasks` command which will list out all the configured tasks.

## Installing Stampede Worker

Now that we have configured a task, we need to run a worker to handle any requests to execute that task. Note that our example task above specified the queue with a name of `tasks`. Note that each task and each worker instance only can be configured for a single queue. But you can have as many tasks use the same queue as you like, and you can have as many workers serving a single queue as you like. For our first worker we are going to configure it to execute from the `tasks` queue.

First to install the worker, use `npm` again. Just execute `npm install -g stampede-worker`. For the worker it is best to create a separate config file for each instance you want to run. So create a `.workerrc` and at least add the following items to the file:

```
redisHost=<host name>
redisPort=<port>
redisPassword=<optional password>
taskQueue=tasks
stampedeScriptPath=<path to script files>
workspaceRoot=<path to workspace root folder>
nodeName=<node name>
workerName=<worker name>
```

The worker will only execute scripts that are located in the path specified in the `stampedeScriptPath` config. For our test task, be sure to create a `test-script.sh` file and put it into the path that you specify in `stampedeScriptPath`.

`workspaceRoot` is a temp folder where the worker will clone repositories to execute commands in.

`nodeName` and `workerName` are just strings that identify the worker when using some of the monitoring tools.

## Executing the worker

Once we have our `.workerrc` file, we can start the worker using the following command:

```
stampede-worker --config .workerrc
```

Now the worker is ready to receive requests and execute tasks.

## Setting up a repository to execute the task

Now that we have a server and worker running, we will want to test it out. For GitHub integrations, you need to configure the stampede GitHub app so it has access to the repository you want to test with. Once that has been configured, the `stampede-server` will start to receive events whenever pull requests, branch pushes or releases happen in the repository. Until the repository contains a `.stampede.yaml` file, however, no tasks will be requested.

For our first task, create a `.stampede.yaml` file in the repository with the following structure:

```
pullrequests:
  tasks:
    - id: first-task
      config:
        task-parameter: avalue
```

This will trigger the `first-task` to be executed whenever we create a pull request in the repository. If all goes well, you should see a check show up when the pull request is created.
