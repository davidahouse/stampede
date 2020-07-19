---
layout: category
title: Getting started
permalink: /gettingstarted/
has_children: true
---

## Introduction

This getting started guide will show you how to setup a very simple Stampede instance connected to your GitHub repositories.

## Prerequisites

To follow along with this guide, you will need to have [Docker](https://www.docker.com) installed. Also you should have a [GitHub](https://www.github.com) account and at least one repository published there.

## Setting up SMEE

First you should setup an account on [smee.io](https://smee.io) and create a new channel. `smee` is a fantastic tool created to help test GitHub apps and webhooks and provides a public URL that can serve as a proxy, letting us route events from GitHub to our local machine and into Stampede. Once you create a new channel you will receive a WebHook Proxy URL. Save this as you will need it to configure your GitHub app and our local instance of the smee client. Don't worry about the rest of the instructions on the smee site as we have that covered below.

## Creating a GitHub app

Stampede implements the GitHub App API, so the first thing we need to do is to create a GitHub app on [GitHub](https://www.github.com). We will use this GitHub app

To do this, go to `Settings` on GitHub and select `Developer Settings`. From here, select `GitHub Apps` and then click the `New GitHub App` button.

Configure your new GitHub app with the following details:

`GitHub App name`: This can be anything you want, for example `My Stampede App`.

`Homepage URL`: This will not be used, so you can use your github account url, for example: `https://github.com/<youruserid>`

`Webhook URL`: Use the `smee.io` URL that you got from the step above. This is where GitHub will send all the events. We will configure smee locally to forward these to our stampede instance.

`Webhook secret (optional)`: You can leave this blank, but in a production setup this is highly recommended.

`Repository permission (Checks)`: Set to `Read & write`

`Repository permission (Contents)`: Set to `Read & write`

`Repository permission (Issues)`: Set to `Read & write`

`Repository permission (Pull Requests)`: Set to `Read & write`

`Subscribe to events`: Select `Check run`, `Check suite`, `Pull request`, `Push` and `Release`

`Where can this app be installed`: Select `Only on this account`

The rest you can keep the defaults.

Once you have created the app, you will need two things to configure your local stampede instance. First, on the `General` page of your app details you will see an `App ID`. Save that number and we will use it in the next step below. Next we need to generate a private key, so select the `Generate a private key` option at the bottom of the page. That will assign a private key and download a `.pem` file to your machine. You will need the path to this file in the next step.

One last task we need to perform in the GitHub UI is to install our newly created app into our personal GitHub org. To do this, just select `Install App` from the app details. You will be presented with the list of all the GitHub orgs that you are allowed to install into. By default you will be able to install into at least your own org that matches your username in GitHub. Click on the `Install` button on your personal org. On the next screen select `All repositories` and then click the `Install` button. Now our app is all ready to send events to Stampede on any repository in your org.

## Cloning the Stampede Example and configuring it

Now that the preliminary setup is done, the next step is to configure stampede to run on your local machine. Note that you can also do this step on a server either on your network or in the cloud. We have setup a sample docker compose configuration that will install all the required components and has them pre-configured to communicate with each other. You can certainly run stampede without docker, but having this compose example script makes this initial setup very easy. Go ahead and clone the repository to your local machine (or whatever machine you want to try stampede on):

```
git clone git@github.com:davidahouse/stampede-example.git
cd stampede-example
```

Next you should copy the placeholder `.env-sample` file so we have a place to put your configuration.

```
cp .env-sample .env
```

Open the `.env` file with an editor and now we can use the `smee.io` and `Github` details to finish the configuration.

```
GITHUB_APP_ID=
GITHUB_PEM=
SMEE_URL=
```

The `GITHUB_APP_ID` is the value you got from the GitHub app details page. And the `SMEE_URL` is the same one you used to configure your GitHub apps `Webhook URL`. The `GITHUB_PEM` needs to be a single string with the contents of the `.pem` file that was downloaded to your machine when you generated your private key. The best way to generate the correct string is to use the following command (substituting downloaded-pem-file.pem with the path to the file on your machine):

```
awk 'NF {sub(/\r/, ""); printf "%s\\n",$0;}' downloaded-pem-file.pem
```

This will output the PEM file with embedded `\n` characters. Just copy paste the output of the awk command into the `GITHUB_PEM=` line of your `.env` file.

Now you are all setup and ready to start your instance of Stampede!

## Starting Stampede

Once everything is configured, starting stampede is simple:

```
docker-compose up
```

You will see all the components start up, and you can stop everything by pressing Control-C. If you want to run the components but not tie up your terminal session, you can append `-d` to the command:

```
docker-compose up -d
```

To stop all the containers when using the `-d` option just use the `down` command from `docker-compose`:

```
docker-compose down
```

To verify that Stampede has started, you can open a browser to the portal:

```
open http://localhost:7766
```

## Configuring a task in Stampede

With Stampede now running, we will configure a sample task that can be executed whenever we receive a GitHub event. The files that we need are included in the `stampede-example` cloned repository.

First we want to activate the Stampede admin mode in the UI, so click on `Login` under the `Admin` menu. The password is set to `example` (don't worry you can change this!). Once you are logged in as an Admin you will have additional menu options under the `Admin` menu.

Select `System Config` and then `Tasks`. Here we want to add in an example tasks that is included in this cloned repository. Select `Choose file` and select the `example-task.yaml` that is located in the `/config/tasks` folder of the `stampede-example` repository that you cloned. Then click the `Add/Update Task` button to add the task. For the curious, the script that will perform this task can be found in `/scripts/example-task.js`.

Next we need to let Stampede know about the queue we want tasks to execute on. Select `System Config` and `Queues` and click on the `Choose file` button. Select the `queues.yaml` file in the `/config` folder of the `stampede-example` repository and click on `Replace Queues`. Now you should see a single queue named `execute` in the list.

Now your Stampede system is ready to perform some work. The next step is to configure your repository in GitHub so it will trigger our test task.

## Configuring your repository for Stampede

There are a few different ways to configure Stampede to run tasks, but for this tutorial we are going to use the method of adding a file to your repository. First pick a repository that you want to test in. For this tutorial it doesn't matter which repository you choose. In this repository, create a branch and then add a `.stampede.yaml` file using an editor and put in the following contents:

```
pullrequests:
  tasks:
    - id: example-task
```

Be sure to save the file to the root of your repository. Commit the change and push up your branch to GitHub. Now create a Pull Request and the check should run!

## Viewing the task results

If everything is setup correctly you will see 2 checks show up for your Pull Request. The first one is titled `Stampede Information` and provides you with a link back to the Stampede UI so you can see the status of the entire build.

![Stampede Information Check](/img/Stampede information check.png)

The second one is titled `Example Task` and is the output from the task we asked Stampede to run on the Pull Request. Now this task is incredibly boring, but if you see it run succesfully then you have setup your Stampede system correctly and now you can continue exploring what all it can do.

![Example Task Check](/img/Example task check.png)

## Troubleshooting

If you didn't see any tasks run on your pull request you should start looking first at GitHub. If you go to your GitHub app that you created and find the `Advanced` page. Here you will see the recent deliveries of the events to the `smee.io` URL that we configured. If you see any have failed, double check your Webhook URL that you defined.

If you see successful events sent from GitHub we should check the `smee` instance that you have running in docker. To check these logs, issue the following command:

```
docker logs stampede-example-smee
```

If things are going well you should see events listed in the log like this:

```
POST http://stampede-example-server:7766/github - 200
POST http://stampede-example-server:7766/github - 200
POST http://stampede-example-server:7766/github - 200
POST http://stampede-example-server:7766/github - 200
POST http://stampede-example-server:7766/github - 200
POST http://stampede-example-server:7766/github - 200
```

If you don't see any events, check your `.env` file to ensure the URL you configured in your GitHub app matches the URL in the `.env` file. They must be the same.

Next up on the list of possible issues is that the `.stampede.yaml` file isn't correct that you added. Be sure to follow correct YAML formatting rules. You can find some YAML format validators online, just copy-paste the YAML and make sure it is valid. If the file has a valid syntax then we need to look at the server logs next. You can view the logs with this command:

```
docker logs stampede-example-server
```

If you still can't get things working, post an issue in the `stampede-example` repository on GitHub and I will try to help you further troubleshoot.

## Exploring what is included in the Stampede Example

You should also dig a bit deeper into the Stampede UI to see what details you can find out about the PR check. Go to the Stampede UI `http://localhost:7766`. You should let Stampede know that your organization in GitHub should appear on the menu. Go to `System Config` and select `Owners`. Click `Add Owner` and select your org that should appear in the list. Click `Add` to confirm. Now you should see your organization show up in the upper left of the Stampede UI under `Repositories`. Select your repository and now you should see your pull request show up in the `Pull Requests` section.

Clicking on the pull request you will be taken to the pull request history screen that will show you all the builds that have executed for that pull request. Based on our default configuration we should see a new build show up each time you push a commit to this PR. For now you should see at least one build. Click on it to dive into the build details. The build details screen will show you the build # and when the build was started, along with the status. The lower part of the screen will show you the list of tasks that were executed in the build. You should see the single `example-task` in the list. Clicking on this task will show the details of the task. Some key notes here is that the task will show which node it executed on, some details about the pull request and the result of the task. Note that tasks have a status along with a summary and text values in the result. The summary and text are updated on GitHub so these should match what you found in GitHub earlier. As for the node, the task actually executed on an instance of the `stampede-worker` app that is running in docker.

To see the logs for the worker, execute the following command:

```
docker logs stampede-example-worker
```

You should see a note about the worker handling the task and then updatating the task with the status completed. One other task that will be useful is to view the working folder for the task inside the worker. Do look at the working folder we need to ssh into the worker container. You can do this with the following command:

```
docker exec -it stampede-example-worker /bin/bash
```

Once in the container, change into the logs folder using `cd logs`. This is the root of the working folders. You will see a sub-folder with the date, explore this folder until you find the working folder where the repository was cloned into. It should be similar to this path: `/var/stampede/logs/7-19-2020/owner-repo/pullrequest-3/1/example-task-0`, where the owner, repo and the pull request number will all be different based on your installation. Looking into this working folder you will see the contents of your repository along with some log files created by the worker.

## Next steps

If you have made it this far, you have setup a very simple, but powerful continuous automation platform. You can now start to build the tasks that match the automations you want to run, and then configure your repositories to run those tasks based on the GitHub events you want them to be triggered on. Some next steps you should look into:

- Create your own task (both the script and the task definition)
- Understand task parameters and where the values can be set
- Create a javsscript based task
- Explore the Stampede UI
- Install the Stampede mobile app
- Expand your Stampede system to run with multiple queues and workers
