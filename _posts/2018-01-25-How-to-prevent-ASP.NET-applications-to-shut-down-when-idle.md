---
layout: post
title: How to prevent ASP.NET applications to shut down when idle
---
If you're an ASP.NET developer, chances are you might have experience that first request to your application takes much more time than subsequent ones. The reason being:

> To save server resources, ASP.NET application pools process shuts down in 20 minutes idle time.

So if there are no requests to your applications in 20 minutes, the application pool will shut down. 

## Problem
This auto shut down is good for you if you're a hosting provider and application share the infrastructure, but if you're not, that might hurt, because the first request will have to wake the process up, Initialize application and then server to the requests.

## Solution(s)
There are 2 approaches:

### Turn this feature off

You can go to application pools' advance setting and set ````Idle Time-out```` to 0 which will mean, application pool will never shut down due to no requests. This is good enough, but you Server Administrators will not agree on this so easily (Well, If they do, you've gat a good team)

### Suspend instead of shut down

Starting Windows Server 2012, there is a newer option to choose ````Idle Time-out Action```` to ````Suspend````. According to Microsoft Docs:
>A suspended worker process remains alive but is paged-out to disk, reducing the system resources it consumes. When a user accesses the site again, the worker process wakes up from suspension and is quickly available.

Suspend option sounds a win-win for both Developers and Server Administrators.

[Step by step instructions](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-85/idle-worker-process-page-out-in-iis85) 