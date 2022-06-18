title: "ROS Performance Boost, An Overview"
categories:
  - Blog
tags:
  - ROS
  - 中文
  - Robot Software Framework
  - Performance Zealotry
---

**Notice** Some information contained in this article has been intentionally obfuscated. This is to protect organization and company secrets according to internal regulations.
{: .notice--info}

## Introduction 
Nowadays robotics systems face a critical problem in real world deployment, performance. But unlike the good old days 50 years ago where there were simply no hardware powerful enough to drive complex robotic systems, today we (the roboticists if I may) are more often limited with budget and cost. These are the factors that affect productions, and I would call it a sweet thing to worry. Just imagine the dark ages our pioneers went through when ideas and dreams never went out of the labs, at best.  

But a worry is a worry, and it is our obligation to sequence computation power out of every clock cycle so more robots are available to the market. This blog would focus on the higher level structural and architectural improvements that can be made to ROS (as well as its lower level components).

## To pull or push

> I'll take what I want

The question of push or pull may sound very strange to average ROS users who may have never looked at the underlying implementation of ROS itself. This is perfectly normal and proves once again ROS has great abstractions. But for those who have dived into ROS and for those middleware vendors the answer would be: it depends. 

Technically speaking, vanilla ROS (at least at the time of writing) uses a pull approach to get data. This means the system is signaled first, so it can decide later whether to take data or not. This sounds perfectly normal but with one twist, in a lot of ROS use cases, the users would want to get the received data ASAP. Signal-and-decide approach would certainly create both CPU and latency penalties under all circumstances. I would love to share data on this but *cough cough I can't help my neighbours cough cough*. One feasible and tested approach is to combine the signal with its corresponding data. That is, somehow access the data when the signal is received and transmit this particular piece of data to user application. For convenience, this process is usually denoted as Expedite Data Handling. 

A very popular example would be in the autonomous driving domain. Images from cameras or PCLs from lidars often require minimum time to process and transmit. Moreover, each and every packet of these data are required by user applications. This is the perfect scenario for Expedite Data Handling and one could expect significant performance improvements even on the whole system level. Moreover, one can apply similar technique to the lower level middleware if it is under control. Guess car companies start to develop their own full stack solution for a reason. 

But be aware, this performance gain only applies to the case where data needs to be processed as soon as they arrive and the user application prioritize speed over agility. For example, if user application uses a timer or `spinsome` then using Expedite Data Handling wouldn't be very desirable. In such circumstances, some or even an extremely large portion of data would not even need to be used. Transmitting them with the signals is simply a waste of resources.

## Performance from conciseness
As mentioned in this [blog](https://omnissiah-enlightenment.github.io/blog/What-really-happens-when-you-start-ROS/). Data reception in ROS is achieved by waitsets, yes, waitset'S'. When reading data from transports, ROS has to use two different waitsets which were basically doing the same thing. The implementation details of the waitsets were also one bottleneck for ROS's performance, but it was fixed around the year of 2020 so no details will be given regarding that. 

Among the two waitsets, the lower level one comes from the RMW implementation, although theoretically one could get away with this waitset if using the RMW implementation does not use one, something similar still must be used. This is one major downside of ROS which its structural design was not very well thought in terms of the performance. But I guess we still have around a decade or so to work on it so thing could get a lot better later. A simple yet powerful way to get this improved is to "combine" the waitsets. This is also how Apex.AI did their magic, according to their [performance test white paper](https://drive.google.com/file/d/15nX80RK6aS8abZvQAOnMNUEgh7px9V5S/view). A big thanks to them which I think Apex.AI was the very first company to focus so much on performance. Wish them the best on their endeavors. 

To do the combination however would require some signification understanding of ROS. As far as I know, one would need to modify the "20% code" part of ROS from inside out. But the result would also be very promising, also it would be a great chance to add customized functionalities to the system. For example, addressing high cost of *cough cough something anything cough cough* development. 

In fact, ROS still has great potentials, and not all of those potentials are from using libraries. The actual framework itself could have a great deal of things to improve. And it is almost certain that some companies have already started to look into building their own system based on the successful exercises of ROS. In those companies if they choose to make one integrated framework, the performance gain would be significant.

## Conclusion

This blogs gives an overview and several examples on how to boost ROS's performance. And for production level systems, performance is often one critical component of success. Just imagine if the user level algorithm could get data at twice the frequency, that would be a light year scale advantage over other competitors.

***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

