---
layout: post
title:  "Vertical Scaling vs Horizontal Scaling"
number: 13
date:   2016-08-18 0:00
categories: reliability
---
The scalability of a system is defined as the ease with which it can be expanded in order to do an increasing amount of work. In the case of a web application, it may be the ability of the application to handle an increasing numbers of users. When talking about scalability, there are usually two ways to do it: vertical scaling, and horizontal scaling.

Vertical scaling involves increasing the power of individual nodes in a system, such as adding more CPU and RAM and making them bigger and fatter, to process and handle more load. It is also referred to as scaling up and scaling down. Horizontal scaling involves adding more, but less powerful, nodes to a system to process and handle more load. It is also referred to as scaling out and scaling in.

Let's say people are standing in a queue in order to go through a doorway. In vertical scaling, you might increase the size of the doorway so that more people can get in. In horizontal scaling, you might create more doorways of the same size.

Both approaches have several advantages and disadvantages. I'd like to point out that no approach is better or worse, and what you choose will depend on your use case and the problem you're trying to solve. Cost and software vendor will also factor in when choosing one approach over the other.

## Considerations
- Horizontal scaling is about adding multiple hardware and software entities to work as a single logical unit. Vertical scaling is about having fewer, but more powerful entities.
- Because horizontal scaling is about increasing the number of nodes, there is no single point of failure. Vertical scaling might create one.
- Horizontal scaling allows you to increase capacity on the fly, while vertical scaling might require downtime. In the context of Amazon Web Services, horizontal scaling might only require increasing the number of machines in an auto-scaling group. Vertical scaling might involve changing the instance type of your machine to something bigger, which would require downtime.
- If you've already planned for vertical scaling, chances are you're saving a lot of state data on your system, like, say, user session data. Saving state data is not recommended in horizontal scaling. What happens if a subsequent request comes to a different machine in the cluster, which doesn't have the same state data?
- In horizontal scaling, machines are considered largely disposable, and are discarded frequently, like batteries. In vertical scaling, machines are considered holy temples, since they contain everything.
- Horizontal scaling is only limited by the number of machines you can connect successfully. Vertical scaling is limited by the amount of CPU and RAM you can add to one node.
- Not all applications will work with a horizontal scaling mode. Throughput and latency needs to be managed more carefully. Vertical scaling applications are usually built with all dependencies installed locally.
- Fragmentation is a problem in horizontal scaling. This occurs when you add servers of different use cases to the same cluster. Let's say 50 machines service one type of request and another 50 service a different one. This means that depending on the nature of incoming requests, different machines will have different utilizations. At different times, some machines may be over-utilized and others may be under-utilized. An important rule of thumb is to separate clusters by use case or application.