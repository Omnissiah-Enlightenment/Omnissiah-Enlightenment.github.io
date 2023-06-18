---
title: "Moving Towards Next Generation Autonomous Vehicle Framework"
categories:
  - Blog
tags:
  - Robot Software Framework
  - EN
  - Autonomous Vehicles
---
> You don't know what you gonna need

It's being quite some time since my last post, COVID and other stuff being made the last months quite harsh. But life goes on isn't it. 

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/2tZjT3L1wD4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

As I wrote in another post in chinese around a year ago, there were many different frameworks available for robots and it was so unclear which one would become the next dominating product like ROS did in the past. And now, it is almost clear that there would never be a next ROS in AV industry. The reason is simple, no two manufacture make the same car. 

## Current Tools

### AutoSar
AutoSar's twin platform strategy certainly showed its ambition to unify the next generation EECS framework in cars. But from the market trends and hiring trends, it is somewhat obvious that instead of a unified framework, AutoSar is becoming more or less a reference for other to modify. Not saying it is bad, nor it made AutoSar not important, but certainly it is not the best possible outcome for them. Having each vendor or manufacture making their own slightly modified version made it impossible for different components to connect seamlessly. It is true that the interoperation protocol can cover most of the situation, but just by having them around made people's lives a lot harder.

### ROS
ROS in AV is at best a demo, this is "THE FACT". One can easily test upper level algorithms and packages in ROS and that is really it. Do not put it into production or anything that could harm people, because it will happen the next Tuesday. It's execution architecture made it very hard to be even close to realtime. Not to mention the performance and uncertainty penalties added in each and every layer of abstraction added for the ease of development.

### Others 
Apollo from Baidu, eCAL from Continental, MotionWise and its OEM variants from ZettaScale, and various in house frameworks from different manufactures. They are somewhat irrelevant, as they are just demos. Some manufactures do have production level frameworks, but as just mentioned previously, they are more or less customized AutoSar like systems, and some even use ad-hoc programs to run autonomous systems.

## Future is Now
So what would the future look like? The answer is simple, it depends. If there is one thing that developed the fastest in the AV industry then it would be the experience on mass production systems. And the result is so surprising which I doubt anyone had foreseen this: no single setup is enough. People used to believe a L4 ready platform would certainly be enough for L2+ tasks. From this and using the effect of scale, we can have a single system to cover everything we need. Sounds fantastic, while the actual execution normally turns into total disaster. In some sense, the two different system requires very different things. Everything on the L2+ system must be extremely cheap so the extra cost would not affect the profit from a car. And for L4(ish) systems, enough upgrade room must be there for potential OTAs. A unified code base can never truly master the different requirements. 

After all, if one still wishes to push to the very limit of AV framework, different platforms with PnP like modules may be the answer. Toggle a set of switches in the codebase to fine tune a framework one solution really needs. Also, it might also be valid to push hardware people to develop more cost-effective chips that can eventually let the shrink the gaps of cost between different hardware solutions. Sadly, considering the cost of getting things done on the hardware side is so high, the future of AV frameworks may still belong to many vastly different types of solutions.

## Closing Thoughts
The field of AV framework has not changed much in the past year in terms of technical advancements. But the mass production experience gained revealed a rather disturbing fact for developers. That is people still have to deal with cross-compatibility in the near future.  


***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.