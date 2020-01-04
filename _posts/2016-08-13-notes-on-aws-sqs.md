---
layout: post
title:  "Notes on AWS SQS"
number: 9
date:   2016-08-13 4:00
categories: cloud
---
Amazon Web Services Simple Queue Service is, you know, a simple queue service. It’s pretty cheap and allows you to decouple the components of your cloud application by allowing you to transmit any volume of data, using unlimited senders and unlimited receivers. Since it allows you to create programs with no direct connections, your programs can communicate with each other independent of time. This means you can create a distributed, decoupled application where your components need to process different amounts of work and different times. You can scale up or scale down based on the amount of work you need done, and leave the messaging system to take care of itself.

## Message Lifecycle
The typical lifecycle of a message in SQS is as follows:

- A system that needs to send a message will select an Amazon SQS queue, and use `SendMessage` to send a new message to it.
- A different system that processes messages needs more messages to process, so it calls `ReceiveMessage`, and this message is returned.
- Once a message has been returned by `ReceiveMessage`, it will not be returned by any other `ReceiveMessage` until a visibility timeout has passed. This keeps multiple computers from processing the same message at the same time.
- If the system that processes messages successfully finishes working with this message, it calls `DeleteMessage`, which removes the message from the queue so no one else will ever process it. If this system fails to process the message, then it will be read by another `ReceiveMessage` call as soon as the visibility timeout passes.
- If you have associated a Dead Letter Queue with a source queue, messages will be moved to the Dead Letter Queue after the maximum number of delivery attempts you specify have been reached.

## Limitations
- Messages can be up to 256 KB and can be stored for maximum of 14 days.
- No FIFO. Message order is not guaranteed.
- Will deliver messages at least once, so duplicates might exist in the system.
- Latencies in tens or low hundreds of milliseconds.
- Allows long polling - listening for new messages in real-time.
- AWS will delete SQS queue if it is inactive for 30 days.
- You can only get the approximate number of messages in the queue.