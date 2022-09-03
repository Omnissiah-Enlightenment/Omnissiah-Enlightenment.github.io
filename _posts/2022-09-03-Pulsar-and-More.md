---
title: "Pulsar and More"
categories:
  - Blog
tags:
  - Edge Computing
  - Cloud Computing
  - Robot Software Framework
  - EN
---

> "Bump-bump-Bump-bump"

## Introduction

In [a previous post](https://omnissiah-enlightenment.github.io/blog/Cloud-on-Edge-robot-in-the-future/), several possible scenarios on how cloud and edge could be fused together to facilitate building of better robot systems were discussed. But in that post, exactly how to achieve the fusion was only broadly mentioned. There have been numerous efforts on putting things together to make such fusion work seamlessly across different deployments and situations. The effectiveness of those efforts still remains a mystery. Very few of them attracted significant attention, both in terms of business and technological.

In this post, yet another possible solution is discussed. But unlike other existing methods, this idea is based on an attempt to create a native framework to be deployed in both edge and cloud environment. Rather than bridging them through another intermediary. And there already exists a new and rather familiar system which is very similar to people who work with high performance cloud computing and core segments of robot frameworks. Apache's latest cloud computing framework(or actually framework used in cloud computing field)--[Pulsar](https://pulsar.apache.org/), is surprisingly a great fit (architecture wise) to the need for a unifi**E**d c**L**oud-**E**dge **C**ommunica**T**ion f**R**amew**O**rk. 

## Pulsar at a glance

![Pulsar System Architecture](../assets/images/pulsar-system-architecture-6890df6b0c59a065a56492659ba87933.png) Photo From [Apache Pulsar](https://pulsar.apache.org/assets/images/pulsar-system-architecture-6890df6b0c59a065a56492659ba87933.png)

The diagram above is the architecture of pulsar. At first, it may seem the system has nothing to do with anything related to robotics and robotics on edge. However, if one is familiar with ROS2's underlying mechanism, it is obvious they are extremely similar in terms of design.

If one ignore the persistence storage and global communication part of the system, what left would just be an in memory system. This system on the other hand can be viewed as a communication middleware with service discovery as an independent module while the reset in charges of actually moving a message from one node/app to the other. This architecture is universal enough such that the basic elements are exactly the same compare to the ones used in DDS, which is ROS2's default communication middleware. 

There are also many other interesting design trade-offs in Pulsar's system, and the details are left for the readers to discover. The only important trait one should note about the Pulsar is it is different from Kafka, which focuses more on providing high throughput on a limited context. Pulsar itself, emphasis more on providing a uniformed handling process for vastly different topics. This is exactly what a unified communication framework should do. Instead of having deployments for each and every topic, they could be hosted in a single logical cluster.

## Edges 

Having introduced the most basics of Pulsar, it is important to note that Pulsar itself is not **THE** framework. It is not fast, as it has persistence and other pure database related system concerns. And it is not realtime enough, using TCP means there would be great cost associated with just establishing and maintaining a connection. So to deploy a uniformed framework, it must meet the strict performance requirements set by the edge side. That is low cost and low overhead. To achieve such goal, the system must maintain most of the traits it is holding right now. For example, it almost has to use UDP and has to use a soft-reliable mechanism rather than a proper high-guarantee delivery contract. 

More over, when such system gets deployed on the edge, it must consider more use cases. For example, how to integrate SHM transport into the system. In cloud computing cases, memory sharing is not a topic as popular as it is in the edge computing area. But if the framework wishes to unify cloud and edge and put both things under its own control, it must find a way to treat various different communication mechanisms transparently. Not to mention the technical difficulties and performance penalties that may incur when different platforms have to come together. Although technically these can be solved by a good service discovery mechanism paired with a protocol strong interoperability. The outcome is still left to be measured. 

On the bright side, such system is not impossible. In communities there have been trails to put modified version of ROS on the cloud (keyword: ROS2, kubernetes, share memory). Although technically the scale and usefulness of existing trails are no match to the massive scale arrays in the data center. They still mark the early lights brought by unifying arguably the two most complex system human have ever built - even if it is just the communication abstraction layer. 


## Cloud(s)

Although Pulsar was built for cloud, this does not mean the cloud side is the easier part to deal with. It has its own big problem that is the performance. Cloud computing infrastructure is fast, but not fast enough for a time and mission critical that potentially could carry people's lives. Today, even the fastest cloud system still relies on internet protocols to communicate with its clients, which means latencies. Although these latencies could be very small they can still be a deal-breaker. And the ways to lower latencies is a forever pursuit for cloud engineers. It is true that the time required to complete tasks has dropped tremendously and applications require extreme low latencies have been migrating to the cloud (cloud games etc.), but typing "lag" to say sorry to teammates is very different from being on a funeral of your car's customer, **very very different**.

After said that though, it is not impossible for cloud to take control. Imagine today where we have our cellphones, and we can video call anyone pretty much anywhere in this world. It was just impossible just a few decades ago (not greater than two). When new communication methods emerge, there will be a way for cloud to connect to edges like PCIe connects to CPU (tried to make better metaphor). And the importance for a unified framework would certainly arise at that time. For now, the framework could just ignore the shortcomings brought by latencies and wait for other technology to advance far enough and ultimately the issue would no longer exists. 


## Conclusion
In this post, a possible framework for fusing cloud and edge together was discussed. Although such system is still only a dream for roboticists, one day it will play an important role in this field. 

***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***


<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
