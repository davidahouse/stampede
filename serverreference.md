---
layout: category
title: Server reference
permalink: /serverreference/
has_children: true
---

## Configuring stampede-server

The stampede-server app has many different configuration parameters that control it's
behavior. The server comes with default for most of the parameters, but a few are required
because they are related to the external redis, github and postgres connections.

To configure the stampede-server instance, there are 2 primary methods. Either you can create a rc
file that contains the parameters and values, or you can pass these via environment variables. It is also
possible to use a combination of rc files and environment variables.

The most common approach is to use an rc file named `.stampederc` in the folder where you run the server from. For example to set the redisPassword parameter the rc file would contain the following:

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

#### Postgres

| Parameter | Default | Description |
|-----------|---------|-------------|
| dbHost | localhost | The host name of the postgres server |
| dbDatabase | stampede | The name of the database to create/use in postgres |
| dbUser | postgres | The user to use when connecting to postgres |
| dbPassword | null | The password to use when connecting to postgres |
| dbPort | 54320 | The port to use when connecting to postgres |
| dbCert | null | If the server requires it, a cert to use when connecting |
| dbLogSlowQueries | "" | Set to true to log any queries to the database that are taking a long time |
| dbLogSQL | false | Set to true to log out all SQL that is executed by the server |

### Web

| Parameter | Default | Description |
|-----------|---------|-------------|
| webURL | http://localhost:7766 | This is the url that the stampede server will be accessible from a brower |
| webPort | 7766 | The webport that will be used when starting the web server |

### GitHub

| Parameter | Default | Description |
|-----------|---------|-------------|
| githubAppID | 0 | The appID given to the GitHub app that you need to create for the stampede system |
| githubAppPEMPath | null | A path to the PEM file downloaded from GitHub for the stampede app you create |
| githubAppPEM | null | Instead of a path you can include the entire PEM in this single parameter |
| githubHost | null | The github host to use for API calls (public github uses https://api.github.com)). For GHE it should be the host name of your GHE instance |

## Misc

| Parameter | Default | Description |
|-----------|---------|-------------|
| logLevel | info | Can be set to verbose for more intensive logging |
| adminPassword | stampede | This sets the password needed to login to the admin mode. You should change this from the default |
| stampedeFileName | .stampede.yaml | This is the name of the repositories stampede config file that will be searched for in a connected repository whenever an event from GitHub is received. |
| defaultBuildRetentionDays | 30 | The number of days to keep the details for non-release builds in the system. When archived, the task details and any artifacts will be purged from the stampede database, while the build and task records will be kept |
| defaultReleaseBuildRetentionDays | 3000 | The number of days to keep the details for release builds. These are builds that are triggered from a GitHub release. In general you want these details kept longer in the system than normal builds. |
| handleQueueHeartbeatNotification | enabled | Set to enabled to cause the server to send out a notification periodically that includes the queue depths. Can be combined with the queueSummaryNotificationURL parameter to send this notification to an external system such as elastic search |
| queueSummaryNotificationURL | null | If handleQueueHeartbeatNotification is enabled, then this parameter will specify a URL that a queue summary will be posted to. This summary can be used to help monitor the system. |
| enableApiDocs | false | Set to true to enable a Swagger UI to be added to the sidebar that you can view the API docs from |
| slackNotificationMoreInfoURL | null | The base URL used when creating the more info link for slack notifications if different from the webURL parameter. Generally this can be left as null and the webURL parameter is used. |
| prCommentNotificationMoreInfoURL | null | The base URL used when creating the more info link for PR comments if different from the webURL parameter. Generally this can be left as null and the webURL parameter is used. |
| workerHeartbeatTimeout | 3600 | The default timeout in seconds before a worker is removed from the list of known workers after it stops reporting a heartbeat |
