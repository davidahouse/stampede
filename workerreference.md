---
layout: category
title: Worker reference
permalink: /workerreference/
has_children: true
---

## Configuring stampede-worker

The stampede-worker app takes most of its configuration for how to run tasks from the
task itself, but there are a few parameters that control the overall behavior of the
worker. The most important are the parameters that define how the worker connects to
redis since this is how the workers communicate with the server.

To configure the stampede-worker instance, there are 2 primary methods. Either you can create a rc
file that contains the parameters and values, or you can pass these via environment variables. It is also
possible to use a combination of rc files and environment variables.

The most common approach is to use an rc file named `.stampederc` in the folder where you run the worker from. For example to set the redisPassword parameter the rc file would contain the following:

```
redisPassword=12345
```

For more details on the possible config file names & locations check out the `rc` node.js module that is used: [https://www.npmjs.com/package/rc](https://www.npmjs.com/package/rc). Note that if you are using environment variables, you need to prefix them with `STAMPEDE_`.

Below contains the reference for each of the servers configuration parameters.

### Redis

Redis is used as a cache in Stampede, and also contains all the worker queues.

| Parameter | Default | Description |
|-----------|---------|-------------|
| redisHost | localhost | The host name of the redis server |
| redisPort | 6379 | The port on the redis server to connect with |
| redisPassword | null | The password to use when connecting to the redis host |

### Worker

| Parameter | Default | Description |
|-----------|---------|-------------|
| nodeName | null | The name of the node that the worker is running on. A single node can have as many workers as needed running on it |
| workerName | null | The name of the worker running on the node. These should be unique on the node so that the reporting can differenciate them |
| stampedeScriptPath | null | This is the path where the worker will find the task scripts to execute. Although the task contains the name of the script to execute, the worker must be able to find the script in the path specified here. Tasks are not allowed to run scripts from arbitrary paths on the node |
| taskQueue | tasks | The name of the queue to listen for tasks on. Each worker can process tasks only from a single queue |
| workspaceRoot | null | The root folder where any git clones will be performed. Note that the worker will automatically create folders under the root for each build |
| logLevel | info | Change to verbose to increase the amount of logs generated |
| heartbeatInterval | 15000 | The time interval for the worker to send a heartbeat to the server |

### Task execution

| Parameter | Default | Description |
|-----------|---------|-------------|
| environmentVariablePrefix | STAMP_ | This is the prefix applied to parameters when creating the environment variables set before executing the task script |
| shell | /bin/bash | The shell to use when executing the task |
| stdoutLogFile | stdout.log | The name of the file that will contain stdout for the task execution. If stderrLogFile is not set, it will also contain the output from stderr |
| stderrLogFile | null | The name of the file that will contain the stderr for the task execution |
| taskTimeout | 1800000 | The number of milliseconds before the task will be cancelled and a timeout result sent to the server |
| artifactListFile | artifacts.csv | The name of the file that can contain a list of artifacts. This list of artifacts will be sent along with the task results |
| taskDetailsLogFile | worker.log | This will will contain details about the task and will be written to the tasks working directory |

### Git cloning

| Parameter | Default | Description |
|-----------|---------|-------------|
| gitClone | https | The default clone mechanism. Note that https is used since the worker receives a token from the server for performing the clone with. If you prefer to have the node contain its own ssh key for cloning you can change this config parameter to ssh |
| gitCloneOptions | null | Any additional options you want to apply to the git clone operation |
| defaultGitCloneDepth | 15 | The default to use with the --depth parameter when performing the clone |
| gitMerge | false | Set if you want the worker to perform a git merge from the target branch before building |
