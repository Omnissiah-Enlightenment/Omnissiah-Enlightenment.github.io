---
title: "What really happens when you start ROS2"
categories:
  - Blog
tags:
  - ROS
  - EN
  - ROS In Depth
---
**Notice** Please realize the content and information of this blog relies on specific versions of software. Follow with caution.
{: .notice--info}

## Introduction

> Let it spin!

ROS is ubiquitous, everyday people use this powerful framework more and more. And in probably every program written with ROS, there is a `spin`. No matter how simply or how terribly complicated function the users have defined, they all have to make it spin. But what really happens with that `spin`? Of course ROS is not powered by hamster wheels. This blog will discuss the actual behind the scene story of ROS's internal mechanism.

Here the `py` version of ROS is used  for discussion, not because it is easy but because it is hard. The framework have to do some extra works to connect the lower level c/c++ written `rcl` to `rclpy`. So if you could follow along the python version workflow you can understand what happens with `rclcpp` easily. Also, it is recommended that you fire up a debugger and step through the process yourself. This blog is not a comprehensive explanation but rather an overview with a little more details.

The ROS program used is `MinimalPublisher`, arguably one of the simplest ROS example. ROS version is foxy, installed from `APT`.
```python
def main(args=None):
    rclpy.init(args=args)

    minimal_publisher = MinimalPublisher()

    rclpy.spin(minimal_publisher)

    # Destroy the node explicitly
    # (optional - otherwise it will be done automatically
    # when the garbage collector destroys the node object)
    minimal_publisher.destroy_node()
    rclpy.shutdown()
```

## Before Spin
In the introduction section above, basic information of `spin` function is presented. But `spin` is not the start of ROS, before that, there is an `init` function that prepares the necessary underlying structures for `spin`. This `init` function does a few very important things. 

### In This Context
The first thing it must do is to get a context. ROS2 wiki [says](https://docs.ros2.org/latest/api/rclpy/api/context.html): "(Context) Encapsulates the lifecycle of init and shutdown.". Very easy, isn't it? Well the actual story is much more complicated. As you follow along the code, you would notice (in most cases) `rclpy` creates a `Context` by calling its `__init__` function in context.py. But in that function, it does not create anything all by itself. It actually imports an implementation and calls yet another function to initialize the `Context`. You would see this line of code.
```python
self._handle = Handle(rclpy_implementation.rclpy_create_context())
```
"Oh it's just another pimpl." No, it is not. It is actually the deep-rooted dark side of ROS. As people all agree that ROS has great modulation, little known was the cost of it. When you have python on top of c on top of c++ on top of c and c++ (not joking here, it is seriously the structure down to rmw layer not including dds), you have to do something terribly terrible. The grimmest and darkest usages in computer systems of our entire history as a conscious specie, `dlopen` that is. ROS uses a similar idea to connect things from different layers. 

Back to the example, this line of code above actually calls a function from another underlying c file called `_rclpy`. This is the connection joint of `rcl` c files to the higher level `rclpy` python files. In `rclpy_create_context`, ROS would call respective functions of `rcl` to do memory allocation and initialization. And eventually wrap the pointer in a `PyCapsule_New`ed object. 

### Context Sensitive
Getting a context is not enough, ROS also has to populate the content of this context. So summarizing the entirety of `Context` with just handling the "init and shutdown" is a bit too over simplified. Continue stepping through the function you would encounter the below line of code also in `context.py`. 

```python
rclpy_implementation.rclpy_init(args if args is not None else sys.argv, capsule)
```

Similar to the function call in the previous code block, this line of code also calls to the underlying c library. But with a twist, this is not the terminus for this function. In `rclpy_init`'s function body there are various calls like `rcl_init_options_init` and `rcl_init`. These function entries, which look quite normal, are where the magic hides. Take `rcl_init` as an example, one will find several functions starts with the designation of `rmw_`. These are the calls to even lower level rmw layer functions. And the actual implementations are vendors dependent. If the debugger being used is powerful enough one would be able to observe several functions in different files sharing the same definition. And their path would reveal these functions come from different vendors. For ROS to know which one to use, the idea of `dlopen` is used again. Through adjusting a `RMW_IMPLEMENTATION` environment variable the framework would know which middleware to use. 

At this point someone would probably be curious of what happens here. Why a function call from `rclpy` eventually gets handled in the rmw layer. Well, remember the emphasis on modularization and layered abstraction from ROS's design documents. They are the direct cause of such complicated function calling structure. Moreover, one may notice that this means, the performance, resources, and other various aspects are NOT under `rcl`'s control. These critical elements depends on the rmw implementation used, and could be drastically different from vendors to vendors. So if one have to forget all but one thing from this blog, remember to choose the rmw and its underlying DDS vendor wisely. They have supreme impact to the system's key metrics. 

After this point, there is not much left to say about the pre-spin `init` function. One can look around the function calls and feel the efforts from the designers to hide as much detail as possible. Be respectful, without them there would be no ROS and our robotics industry can never be this prosperous.

## Spinning

Now we can look at what this `spin` function actually does. Hopefully the previous chapter has already given a good warm-up and basic knowledge of ROS2's internal structures. 

### Executor! 
> We need better executors.  -- Every ROS user with performance concerns

Executors are critical in ROS, they are the actual "things" which, well executes both internal framework tasks and external user defined callbacks. For more information about them, one can consult this [article](https://docs.ros.org/en/foxy/Concepts/About-Executors.html) which gives more detailed discussion. The focus of this blog is not executor classes so details will not be presented here. One only needs the idea of executors to continue reading.

Similar to context initialization, the executor used need to be created. The default choice is a `SingleThreadedExecutor`, which has some significant issues. But there is no doubt this type of executors is functional and the discussion of different executors is out-of-scope here. When creating the executor, there are several interesting things to note. The most important one is the presence of `GuardCondition`s. This group serves as a key element in ROS's lower level code for information transformation. Here it is used to identify whether the `WaitSet` should be rebuilt. Speaking of `WaitSet`, it is the core mechanism that moves ROS and the representation of the framework's "pull style" process logic. More discussion will be provided later. Also, please note there would be a second `GuardCondition` registered to handle SIGINT situation. So don't be surprised when more than one `GuardCondition`s were observed. 

Another point to note for people who were familiar with ROS1, now the API actually checks for `ok()` by itself. One can see a few of them in the code flow. It is a better encapsulation effort of the code to save those meaningless seconds spent on the "while-ok" loop.  

### Execute Order ~~66~~

After setting up the executor, the node which the used created in the program would be added to the executor. Then the executor would `spin_once` in a while-ok loop. This `spin_once` function is really just a wrapper around a gigantic wait function. Which would use the aforementioned `WaitSet` to track the changes that happen in the context of this domain(not every type of change). At least the higher level design expects this such behavior. The actual implementation, as one may expect after reading till this point of the blog, depends on the actual vendor's rmw implementation. FastDDS uses builtin reader entities while CycloneDDS uses a caching mechanism with an acting pooling thread. 

Back to the `WaitSet` itself, six different types of entities can be listened. `Subscription`, `Timers`, `Clients`, `Services`, `Guards`, and `Waitables`, if one is familiar enough with ROS's communication mechanism it would be obvious that these concepts come from ROS's communication building blocks. Except the curious `Waitable` here, it does not belong to any group of ROS's communication. This type of entities is actually a higher level idea compared to other categories, as the other five class of entities are all `Waitables`. So theoretically, the only type of entities which the `WaitSet` should handle is the `Waitables`. The reason why the other five types of entities are still not replaced by `Waitables` is probably the amount of work required.

The trigger of one entity normally would be caused by a change in the rmw layer. There when DDS receives a new message it would signal the `rcl` that some information is available. After a `WaitSet` entity gets triggered, ROS would do work based on the category of the triggered entity. There would normally be a take to get data from DDS followed by a series of user defined callbacks. This part is rather straight forward but be sure to realize `rmw` and the underlying DDS implementation plays a huge role here. They would together to define the speed, resources, and pretty much everything one would care about if one digs this deep. 

To summarize, the fundaments of ROS is a while loop which actively checks for changes in the system and execute tasks based on the triggered category.

## Conclusion

This blog gives a gentle overview on the internal mechanism of ROS. The initialization of ROS and actual implementation of `spin` function was presented. As mentioned previously, if one really wants to remember just one thing. Remember to choose your DDS vendor wisely. 

***"Hail the Omnissiah! He is the God in the Machine, the Source of All Knowledge."***

---

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.