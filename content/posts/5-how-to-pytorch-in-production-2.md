---
title: "How to Pytorch in Production: Architecture"
date: 2019-03-26T08:32:36+02:00
draft: true
description: "
What do you do when your model is ready? How to optimize it, how to serve it, how to deploy it.
We've gone from a prototype to a production ready API. Here is how..
"
---

Recently I was giving a talk on one more of deploying PyTorch models into production. You can find slides here [Event Driven ML](https://pyconodessa.com/public/docs/slideshare/taras_matsyk_-_event_driven_ml.pdf).
In short we've started with a simple prototype running on Tornday and AWS and got to the point where we can scale services in a matter of seconds. Let me share my thoughts on the journey.

### How it all started?

Or, how to build a prototype in a day.

Let's be honest, whenever a business person comes to you and says: "I want to build self-driven car, can you do that?". Of course as a software engineer we have a solution. 

What do we do? Me, personally, open google and type "stackoverflow how to self-driven car" -> hit enter.
The very first answer will tell you to use JQuery, the second one will give you a hint on where to start. That's how we approached an image recognition challenge and the start was pretty simple, here is a set of technologies that were chosen:

[![Screen-Shot-2019-03-26-at-10-11-01-AM.png](https://i.postimg.cc/mgBqhrWC/Screen-Shot-2019-03-26-at-10-11-01-AM.png)](https://postimg.cc/QHYSyhtd)

I bet it seems pretty obvious to you, here is what you get:

- AWS with GPU for serving PyTorch models
- Tornado on top of it to handle 10K
- Redis for caching predictions

To be honest such setup could classify around 200 images per minute and if I would be asked to come up with a PoC for another model any python async framework with Redis living on AWS would make a deal.

#### Where the number 200 comes from?

It all depends on metrics, here is what we defined as successful image processing. Our classification process is next: You send us an image with a `POST` request, then you use `GET` to receive results. If results are ready - image was processed fast enough. Usually it takes between 1 and 2 seconds.
Considering this requirement Tornado with a PyTorch running in a ThreadPool could process 200 images per minute. It worth to mention that we had 8Gb of GPU memory and around 20Gb of RAM, 5Gb of which was consumed by a server due to threading pool and queue for caching.
If you are wondering why we did not use ProcessPoolExecutor - that's because of PyTorch, it did not play well with python concurrency and it does not play well now. Considering you have to take care of memory sharing.

#### So what? Is there a problem with current approach?

Well, to say the least, AWS is expensive, threading is not parallelism and Tornado monitoring is hard. That's where we started thinking on building an extendable distributed system.
Here is how original plan looked like:

[![Screen-Shot-2019-03-26-at-11-18-41-AM.png](https://i.postimg.cc/T32K0rXw/Screen-Shot-2019-03-26-at-11-18-41-AM.png)](https://postimg.cc/pp7VX5rb)







