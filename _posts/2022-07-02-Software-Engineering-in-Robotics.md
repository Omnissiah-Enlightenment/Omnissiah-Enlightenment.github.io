title: "Software Engineering in Robotics"
categories:
  - Blog
tags:
  - Software Engineering
  - EN
---
**Notice** Opinions shared in this article are pure subjective to author's view and do not reflect any organization or entity.
{: .notice--info}



> "A feeling so powerful that it transcends words, and must be conveyed in another form."

## Introduction
It may seem strange to explicitly mention software engineering in the context of robotics. It is completely valid to argue there is no fundamental difference of a software designed for robotics and Fortnite. The truth is, however, much more complicated than anyone out of this field think.

The simple answer would be: Moore's Law does not apply. You'd probably shocked to hear companies (the hoarders) are trying to run a full L4 AV stack on a system less powerful than your car's infotainment system. Like, you have to do slam, fusion of twenty-some sensors, and planning on a board with 2GB of memory. This means extreme efficiency and cleanness of code, which unfortunately is very unlikely. This blog will discuss several lessons learnt to improve the quality of life for average software roboticists. 

## Never cringe when you are right
It is quite common for roboticists to be in an unfavorable position. As "fancy" algorithm people often make inappropriate demands either because their inability needs an excuse or because their plain stupidity. The most common example would be configurations, a properly designed framework may present a large range of different configuration options for users to tune. So the behavior, such as performance-efficiency, could be optimized based on the usages. But the users, at least a portion of them, never wants to configure them. One of the most "triggering" demand ever went out of these people is "Why you can't do that yourself". Look, the framework is being used in vastly different environments. Some were deployed in distributed systems for optimal scalability while others were deployed on a single chip for optimal performance. Even adaptive configurations need some sort of external input. Needless to say the complexity to bring in an automatic configuration system. 

The story does not end here. The manager in charge heard the debate which was unsettled at that time and decided to do what ever the users want. That "Give them what they want" style of thing ultimately upset pretty much everyone in the team. One committer and one PL left the team shortly after the person declared his decision as final. The reason is fairly easy, frameworks are extremely complicated systems and their structure has to be carefully thought through. Not only this means it has to be designed well, but also means to be maintained well. Adding functionalities, not to mention external functionalities, must be extremely conservative. If it is inappropriate to put something into the framework level, don't put it. If a not well-thought functionality is added to the framework then the users would have a million ways to exploit the framework, and it would be impossible for the team which maintained it to recover without extensive fights.

## Code, CoDe, CODE!!!!
Code quality is your life, and this is especially true in robotics. Unlike other "high-tech" companies which produce nothing but an interface for funny videos, code for robots may determine a lot of people's lives, soon more of them. The quality of code should arguably be even higher than that being used in rockets. In fact, though, it is apparent that you can certainly "trust" companies which cut corners on hardware to put high standards on software. More obvious is that very few if any of those commercial "robotics" companies are willing to put any critical resources on the software stacks. When everyone is going on a hype for fancy algorithms and improvement done by P-hacking, few in the industry could still hold the bar for code quality. If it runs, it's good; if it passes a test set, then it's bulletproof and ready for commercial uses. But do remember what nowadays robots are, they are no longer in labs and people will not forgive mistakes made in the real world.


Thing could go even worse for industries with intensified competitions, autonomous driving for example is a perfect example. Put the dark fact that the algorithms are validated by human souls aside, in this area you could find some of the craziest steps taken to maximize profit from safety. For the sake of personal safety the details will be kept secret, but I know I would never sleep as well as before. And there is a tip for those are interested in purchasing those cars. Look at how many people in the company buy the car they make. If very few cars are purchased by its employees, one would know which position or attitude they have towards the software they write by their own. And if any internal information gives negative opinion on buying a car, don't ignore them. These may be some of the most sincere words a person could give. Under the shinny finishes of artificial intelligence only those who faces the code every day could imagine what can possibly go wrong during one's driving.

## Go beyond code
Code itself is not enough. One interesting argument is if one has to delete either code or other materials, people in robotics area probably would choose to delete code. This is because very few systems require the same level of logical rigorousness as robotic frameworks. Below this there are countless lower level tricks and above this layer there are billions of ways users could interact. And as a framework, all of these has to be managed neatly. The code itself may ultimately turn into complex logical expressions which are extremely difficult to reason. And don't get it wrong, it is not the programmers' inability to make the logics clear, it is the logics itself are too complicated to be in the "few other aspect" category of any code tutorial. Thus, the ideas and proofs which support the framework become even more important than the code for framework itself. And this is a lesson learnt the hard way. Do not wait, when one faces an absolute need to build such knowledge hub it could only mean it is too late.

Another important thing is communication, it is true that in some company R&D personnel are completely isolated from the outside. Normally this is to keep them away from potential outside recruitments. But this also makes it very hard for them to really realize what is necessary. Information get lost in translation and no one could make sure the lost bits are not the most significant ones. So keep in touch with the end users and listen to what they want is essential. This is arguably one of the most important things in software engineering and can be even more important in robotics industry. But remember, do not let them take the control. In most cases the users know absolutely no insight on the framework and the hard work one puts in to make sure things goes as smoothly as it could be. And essentially the same goes for the lower level systems, remember to keep in touch so the framework could get benefit from the decisions they make. Few things could demand more collaboration than a robotics framework.


## Conclusion

This blog shares some lessons learnt and some important advices for developing robotic frameworks. Software engineering is hard, it is even harder when combining with robotics. Keep it up.  

***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.