---
title: "ROS的其他选择"
categories:
  - Blog
tags:
  - ROS
  - 中文
  - Robot Software Framework
---

**注意** 本文中出现的观点仅代表个人而非任何与作者现实中产生交集的人或组织。
{: .notice--info}

## 引子

> 我永远都有选择

正如在本系列[之前一篇](https://omnissiah-enlightenment.github.io/blog/ROS2-the-spine-of-robots_cn/)中所说的，ROS并不完美，甚至可以说是充满的缺憾的。与其无与伦比的适用性相对的，则是在很多情况下它并不能做到最好。诚然无数的高质量拓展大大缓解了这一问题，但是某些情况是无法考上层的应用去弥补的。最简单的例子来源就是自动驾驶（咳咳，不能多说，不然要被抓了），ROS基本无法满足自动驾驶中快速性，可靠性，和即时性的要求。当然这并不代表ROS不能用了，例如Indy Autonomous Challenge里车辆都经过一系列严苛的考验。不过总的来说，确实存在各种在某些场景下架构大大优于ROS的系统，他们大多都是为了某些特定需求而量身定做的或者是由某些工具特化而来。在这篇博客里我会对他们进行一些简略介绍，如果有机会的话会对几个颇有潜力的系统进行更详细的叙述。

## 特化的工具
本段将介绍一些特化转为某些场景设计的通信框架，它们大都只会考虑到某些场景的应用而并不会考虑将工具或者平台泛化到其他应用场景里。当然，这也是非常正常的，毕竟大部分这类工具的起源都是“我只要这个就够了”。

### ZeroMQ
[ØMQ](https://zguide.zeromq.org/) 可不是起源于“我只要这个就够了”而更多的是“你这个不行”。 它其实是一个非常之泛化的工具，可以使用在几乎任何你需要通信的地方。但是这里称其为特化的工具则是因为它特化于泛化（Generalization-Specification相对论）。它只是一个单纯的消息传递系统，适用性超级广泛，而由于这些也导致了它没法提供任何方便的函数或者其他东西来简化开发流程。理论上来说ZeroMQ的背景消耗，例如保持通信等等的消耗是非常之低的。最重要的是它的背景带宽非常之低，恰巧是服务器场景中最宝贵的资源（第二宝贵的是机架之间走廊的宽度）。

对于机器人应用来说，引入ZeroMQ不是什么新鲜的事情，早在ROS2依旧在襁褓之时OSRF的大哥们就做过ZeroMQ的原型。具体信息可以参考[这篇文章](https://design.ros2.org/articles/ros_with_zeromq.html)，这篇文章已经很清楚的叙述了ZeroMQ的优势与劣势，本文也就不再赘述。除此之外，还有一个更有意思的文章，是Fast RTPS与ZeroMQ的性能对比，在[这里](https://www.eprosima.com/index.php/resources-all/performance/zmq-vs-eprosima-fast-rtps)你可以看到哪怕是在对ZeroMQ最不利的软可靠场景下，DDS依旧只能和ZeroMQ55开。所以理论上来说在非可靠和（DDS根本就没有的）强可靠场景下，ZeroMQ会有很大的优势。

### PX4

> “瑞士是世界上最完美的国家” --Youtube 播主

ETH Zurich盛产猛男，而[PX4](https://px4.io/)就是来自苏黎世的猛男的大作。我毕业于ETH Rejected的学校，所以我自认为没有能力去锐评PX4这套东西。不过好在PX4基本是应用非常广泛非常亲民的框架，有数不胜数的各类文章和教程，在这篇文章里我也就不献丑了。

### LCM

[LCM](https://lcm-proj.github.io/tutorial_general.html)的来头不小，最初是由Standford的学生在参加DARPA Urban Challenge的时候开发一套专为他们的自动驾驶系统设计的框架。在当时，甚至ROS1的第一个版本可能都不存在。LCM是一个相当早期的平台化通信的尝试，但是它具备非常多我们现在依然在依赖着的系统的底子。从它的整体结构来看，它没有像ZeroMQ之类的东西一样用一套自己定义的数据结构，而是像DDS一样用了一套Language Independent Data Structure。这样的话就不需要费心费力的考虑额外的扩展性等等。这一套东西和ROS的整体设计也非常非常的像，甚至我怀疑ROS的雏形就有来自LCM的大哥参与。不知道他们对现在ROS2的充斥着低效冗余的结构会怎么看。

在我看来，LCM可能是你能找到的最像ROS的东西了，甚至可以说是ROS0.1？这套东西给了我们一看探究ROS本质的机会，不必为了那些已有的非常fancy各种用途迷失了目的，我们应该做的就是把数据用一种上层算法能够高效理解的方法从一端传递到另一端。除此之外的其他一切都是锦上添花，而假如做不到这一点，那其他所做的一切都是无根浮萍。

## 专为未来而生
以上说的那些都是和ROS同世代的不同方案，但是历史的车轮滚滚向前，ROS虽然足够通用但是它并不能完美的满足所有需求。于是有很多基于更新方案的系统被开发了出来。他们中有些并没有ROS的继承性包袱，所以可以基于旧世代所没有预料到的技术来获得更好的性能和功能。

### eCAL

[增强型通信抽象层](https://continental.github.io/ecal/)也就是eCAL是大陆汽车开发一个基于共享内存和UDP进行通讯的中间件。最初的起源据说是大陆汽车内部开发自动驾驶系统的团队做的一个转为图像和大吞吐量的数据设计的通信模块（这个需求在几年前甚至都是不存在的）。因为对速度和性能的需求，eCAL把共享内存的重要性提高到了非常高的高度。相对来说，ROS虽然也有共享内存（iceoryx）但是那个共享内存方案更像是一个外挂的模块而已。不过这个也是非常正常的，毕竟ROS需要兼顾到多机器人的场景而对自动驾驶来说，一个主要的芯片来承载那些视觉的等等运算就已经足够了。多出来的UDP功能也可以在通过lan链接的系统里提供全局通信的支持（当然你也可以力大砖飞用多芯片上自动驾驶，不过我的评价是弱智中的弱智才会用）。

在我看来，eCAL的潜力的巨大的，或者说我认为ROS应该像eCAL这样发展。把共享内存作为一等人来对待可以让整个组件的通信效率上升非常多。而以共享内存为中心同样会为整个架构打好基础，lan通信过于灵活了，以至于什么样的人都能写出能动的代码。只要有一个这样的人出现在团队里并且作为一开始的几个人之一把自己的东西拉进去了，那么之后要清除这些影响要耗费巨大的精力。共享内存的话则需要对整个链路做透彻的了解才能真正运行起来，于是那些黑天鹅自然没法在这里胡作非为。这也为整体代码的质量起到了一个看门的作用。

### Isaac

对英伟达的[Isaac](https://developer.nvidia.com/isaac-sdk)来说。。。好吧我根本说不了啊，这玩意是个黑盒而且主要是做为AI工具箱出名的。我只能说，皮衣刀客，你不是人。

## 总结

这篇博客简单（非常的简单）介绍了几个ROS的替代品，他们都有各自的特点，不过其实没有一个可以真正和ROS抗衡。假如像超越ROS，那么必须有跨时代的功能。

***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.



