---
title: "Zero-Copy and No-Serialization"
categories:
  - Blog
tags:
  - EN
  - Robot Software Framework
  - Performance Zealotry
---

> Power! 

## Introduction

As mentioned in a previous post, robotic systems and in fact almost all software systems are on a forever pursuit for higher performance. This means more throughput using the same piece of hardware or same performance with a lower resource consumption. If one have ever dived into this topic then it maybe apparent that achieving such goals is not easy. In robotic system the room for make changes is tight. Unlike proper software running on proper machines, software for robots have to consider the hardware used. And most of the time, dirty hacks related to specific hardware platform is a necessary to achieve desirable performance. 

But there are, indeed, several key performance improvement actions which are platform independent and more or less use-case independent. These actions can be considered as "free lunch" for anyone who are extremely concerned with performance. And should be categorized as common practices in most cases. Here the most common and arguably the easiest two is discussed, zero-copy and no-serialization. These two methods are widely used in pretty much every high performance robotic framework one could possibly think.

## Zero-Copy

Zero-Copy means, well, no copy of data is made. But to be more precise, it maybe defined such that from the first producer to the last consumer of a particular piece of data, the exact same piece of data (content and memory address) is used. But of course this is a rather strict definition compared to what most production system really uses. For example, in ROS2 the so-called zero-copy mode is only applied to the RCL layer. It is true that in theory the middleware should support zero-copy in its internal mechanisms but in fact most systems do not provide such capability. There is also another issue with ROS2's zero-copy which it only supports zero-copying between two entities. If the data needs to be passed around along a long chain of entities, there almost certainly will be copies. It is true some really advanced and well written middleware can pass data around without even a single copying, but in most cases that would involve, as one may already guess, complex low-level kernel hacks and even hardware couplings. 

However, even if most zero-copy solutions are not "true zero-copy" as we defined earlier, they still have great impact on overall performance. For example, as much as 80% of transport time can be saved by using zero-copy messages in ROS2 as described [here](https://discourse.ros.org/t/using-zero-copy-transport-in-ros2-with-ros2-shm-msgs/26226). On the other hand, This could give average people a basic idea of how good the return of zero-copy is. In time critical systems, zero-copy can almost be considered as a silver bullet to reduce latencies. Also, it is important to note that for some communication means, it is impossible to achieve true zero-copy, for example ethernet connections. But there are still ways to practice some zero-copy to reduce latencies. For example the `vmsplice` call and `recvmmsg`, these two serves two different purposes. The first is to eliminate the copy within linux kernel, but it may involve nasty security and safety issues. The second is used to reduce kernel system call to increase performance, more information is available [here](https://man7.org/linux/man-pages/man2/recvmmsg.2.html). But if one is using customized it is the best to consult the OS people directly and push them to do similar stuff. After all, these methods can come in very handy and the issues related could be avoided if the whole system operates under a controlled environment. 

## No-Serialization

No-serialization on the other hand is a rather new idea for average people. It used to be the secret ingredient for hedge fund companies to gain timing advantages in trading. For these companies, a single millisecond or even several microseconds level advantages could mean huge profits. And the story of how they include speed of light in their system latency estimation is not a joke. No-serialization does not only help to reduce latency but also helps to reduce CPU cost, a `static_cast` is faster than any serialization method, and it **is** correct. But the question remains to be, how to use no-serialization methods. Although technically, no-serialization has a wider range of possible environments to be deployed. Because technically as long as the message sent can be correctly interpreted by the receiving end, there is no need to have a serialization/deserialization process. The actual uses are actually much more complicated. For communication within system using shared memory systems there should be no need to do serialization at all. The difficult part is how could the system use no-serialization across ethernet and across different types of endianness. The answer would be using a customized protocol which would include endianness and other factors in consideration. 

It is important to note to achieve all around no-serialization the system must be robust enough to handle various edge cases which may occur. But the actual scenarios could vary a lot depending on the use cases. For example, if deployment environment involves boards with different architecture or endianness it may be the best to have the customized protocol to include a way to either only send no-serialization data to endpoints with the same endianness or have the endpoints aware the corresponding endianness and act accordingly. This entire process is rather easy to do and can be integrated into existing protocols as extensions. But do aware the interoperability issues brought by this approach. On the other hand, it is doubtful if interoperability should still be so much of a consideration if one is already building a customized protocol. And in real world productions, the interoperability literately is not a even a thing. Each and every company has its own little standards, and a company does not pay enough attention, the self defined standard may already go against well established protocols. 

Also, although "true no-serialization" is hard and may involve many issues, reducing the number of serializations is a doable approach. There are already attempts to do partial serialization or using an independent serialization module across different system to reduce cost. For example, this [paper](https://arxiv.org/pdf/1810.00556.pdf) presented a rather naive way to reduce the need for serialization. 


## Conclusion
In this post, two useful methods to improve robotic system performance is discussed. Although they are not perfect by their own, most robotics system could still benefit from employing such methods. 


***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***


<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
