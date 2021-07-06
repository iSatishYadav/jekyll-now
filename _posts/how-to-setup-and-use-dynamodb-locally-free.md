---
layout: post
title: How to setup and use DynamoDB Locally - Free
tags: ["aws", "dynamodb", "docker"]
---

# How to setup and use DynamoDB Locally - Free

Did you know DynamoDB from AWS has a locally downloadable version which you can install on your local machine and use for development and testing purposes.

As a newbie to AWS, I was unable to find any comprehensive guide on how to setup and use DynamoDB locally. So without further ado, let's start.

## Download the DynamoDB Local (Downloadable Version)
There are 3 ways you can download:
1. Standalone `jar` file
2. Using `Docker`
3. Using an Apache Maven`

I'm using Docker for this tutorial, for instructions on other 2 ways, visit this official [AWS site](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

> Please note your application doesn't have to be a containerized application, we're just using DynamoDB database as a container, that's it.

## Using `Docker`
Make sure you have docker up and running. If you've not, follow this [official instructions](https://docs.docker.com/desktop/)

````shell
docker run -p 8000:8000 amazon/dynamodb-local
````

This will download `DynamoDB` locally from official Docker image and run.
If everything is okay, you'd see output something like this:

````shell
Initializing DynamoDB Local with the following configuration:
Port:   8000
InMemory:       true
DbPath: null
SharedDb:       false
shouldDelayTransientStatuses:   false
CorsParams:     *
````

This would run the fully functional DynamoDB instance at `http://localhost:8000`.

## [Bonus] Verify installation using AWS CLI.
If don't have AWS CLI installed, follow [this official guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

Run this command to verify the installation

````shell
aws dynamodb list-tables --endpoint-url http://localhost:8000
````

If you get an error like this:
````shell
You must specify a region. You can also configure your region by running "aws configure".
````
You can run:

````shell 
aws configure
````
And you'd be asked to setup follwoing:

````shell
AWS Access Key ID:
AWS Secret Access Key :
Default region name [x]:
Default output format [None]:
````
You can enter any values, as you're only using DynamoDB locally.

That's it!

That's how you can configure AWS DynamoDB on your local machine to develop and test your applications without spending anything.

Happy Coding!
