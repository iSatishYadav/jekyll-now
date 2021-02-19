---
layout: post
title: How to create MSMQs in your Windows 10 local development machine (2 ways)!
---
<p>
Some point in your development career, you'd stumble upon an application which requires MSMQs (Microsoft Messaging Queues). Here's how to create MSMQs in your local development machine.
</p>

## Enable Microsoft Message Queueing Feature
Microsoft Message Queuing feature is disabled by default, you need to enable it.

* Search *Turn Windows features on or off*

    ![](/images/turn-features-on-or-off.png)

* Enable *Microsoft Message Queue (MSMQ) Server*

    ![](/images/microsoft-message-queue-enable.png)

This will search for some files in Windows Update and enable this feature.

## Create an MSMQ using UI

* Search *Computer Management*

    ![](images/computer-management.png)

* Scroll down to *Services and Applications*-> *Message Queuing*
  
    ![](/images/create-private-queue.png)
 
* Right Click either on *Private Queues* or *Public Queues*-> *Private Queue*
* Give the MSMSQ a name. Check *Transactional*, if you want to create a *Transactional Queue*.
  
    ![](/images/demo-queue.png)

* Newly created MSMQ will be listed.

    ![](/images/demo-queue-listed.png)

## Create an MSMQ using PowerShell

* Search _PowerShell_

    ![](/images/powershell.png)
* Type following command

    ````shell
    New-MsmqQueue -Name ps-demo-queue -QueueType Private -Transactional
    ````
    ![](/images/new-msmqqueue.png)

* You should see output something like this:

    ![](/images/new-msmqqueue-output.png)
* You can verify the MSMQ created

    ![](/images/ps-demo-queue-listed.png)

That's it! Now you know 2 ways to create an MSMQ.

Happy Coding!
