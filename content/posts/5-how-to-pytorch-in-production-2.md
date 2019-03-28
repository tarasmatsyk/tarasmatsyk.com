---
title: "How to Pytorch in Production: Architecture"
date: 2019-03-26T08:32:36+02:00
draft: false
description: "
What do you do when your model is ready? How to optimize it, how to serve it, how to deploy it.
We've gone from a prototype to a production ready API. Here is how..
"
---

Recently I was giving a talk on one more of deploying PyTorch models into production. You can find slides here [Event Driven ML](https://pyconodessa.com/public/docs/slideshare/taras_matsyk_-_event_driven_ml.pdf).
In short we've started with a simple prototype and went to a production ready scalable solution. In this post I want to share our discoveries.

### Who we are and what we do

Basically, Lalafo is a classified. If you want to sell an old phone - you look for us. This is what moder paper advertisement become. 
However, business model is pretty simple here, what we really want to do is to take it to the next level. So far, regular posting time takes around 2 minutes. What we want to do is to take it to the next level.
To achieve it we need to be much more technological company that any competitors over the world. One of our helpers is AI, which is used to fill post with price, description, category by photo and a data we have about user and similar items.
Here is how the end goal looks like (in short: you take a photo - we do the rest):

[![Screen-Shot-2019-03-28-at-6-10-00-PM.png](https://i.postimg.cc/mgDRwzsN/Screen-Shot-2019-03-28-at-6-10-00-PM.png)](https://postimg.cc/9rvvXfzr)

So far, we are integrating everything in the next version of the mobile app and performing user testing to make sure it is a fire.

### How it all started?

Or, how to build a prototype in a day.

Let's be honest, whenever a business person comes to you and says: "I want to build self-driven car, can you do that?". Of course as a software engineer we have a solution. 

What do we do? Me, personally, go to the google and type "stackoverflow how to self-driven car" -> hit enter.
The very first answer will tell you to use JQuery, the second one will give you a hint on where to start. That's how we approached an image recognition challenge and the start was pretty simple, here is a set of technologies that were chosen:

[![Screen-Shot-2019-03-26-at-10-11-01-AM.png](https://i.postimg.cc/mgBqhrWC/Screen-Shot-2019-03-26-at-10-11-01-AM.png)](https://postimg.cc/QHYSyhtd)

I bet it seems pretty obvious to you, here is what you get:

- AWS with GPU for serving PyTorch models
- Tornado on top of it to handle 10K
- Redis for caching predictions

To be honest such setup could classify around 200 images per minute and if I would be asked to come up with a PoC for another model any python async framework with Redis living on AWS would make a deal.

#### Where the number 200 comes from?

It all depends on metrics, here is what we defined as successful image processing. Our classification process is next: You send us an image with a `POST` request, then you use `GET` to receive results. If results are ready - image was processed fast enough. Usually it takes between 1 and 2 seconds.
Considering this requirement Tornado with a PyTorch running in a ThreadPool could process 200 images per minute. 

It worth to mention that we had 8Gb of GPU memory and around 20Gb of RAM, 5Gb of which was consumed by a server due to threading pool and queue for caching.
If you are wondering why we did not use ProcessPoolExecutor - that's because of PyTorch, it did not play well with python concurrency and it does not play well now. Considering you have to take care of memory sharing.

#### So what? Is there a problem with current approach?

Well, to say the least, AWS is expensive, threading is not parallelism, Tornado monitoring is hard, scaling is not so obvious as well (just drop load balancer on top of N ~~workers~~ servers, we call it python way), ML results are not persistant etc. That's where we started thinking on building an extendable distributed system.
Here is how original plan looked like:

[![Screen-Shot-2019-03-26-at-11-18-41-AM.png](https://i.postimg.cc/T32K0rXw/Screen-Shot-2019-03-26-at-11-18-41-AM.png)](https://postimg.cc/pp7VX5rb)

We've had a few goals to achieve:

1. Monitoring. (Send data points into influx and use Grafana for monitoring)
2. Save some $. (We had a change to adopt K8s and selected Hetzner as a bare metal with linux on it provider)
3. Being able to scale on request
4. Have fun (We decided to adopt Go for data processing and API)
5. Persistence (PSQL was chosen to store classification results so we can build reports and analyze history)
6. Extendable (If we decide we want to add more ML - we should be able to do in a shortest possible term)
7. SDK-friendly (API users should be happy as well)

### Where did we end up. Let's review components

Here is what we ended up with:

[![Screen-Shot-2019-03-26-at-11-36-43-AM.png](https://i.postimg.cc/ZqrXqyC8/Screen-Shot-2019-03-26-at-11-36-43-AM.png)](https://postimg.cc/Y4Cdn0N0)

Let's review difference and components one by one

#### API VS ML

The main desicion was to split interaction with clients and data processing layer. In the end we've got Go+Swagger+PostreSQL API which consumes around 20Mb or RAM, works extremely well and can be scaled by 10x without any thoughts abount new servers (yes, we have a limited data center capacity, however K8s solves it with adding extra capacity).

The question comes what to put in between? We had different options, starting from Redis, RabbitMQ, HTTP/gRPC and ended up with Kafka. The main 2 advantages are: easy to scale with partitions and message buffering. The main disadvantage is rebalancing. If you used it before - you know it, otherwise please test Redis or RabbitMQ before you implement your own message bus.

#### Monitoring

As we had an Influx server available it was a pretty reasonable choice to send some data points and monitor them using Grafana. Here is how live system looks like atm:

[![Screen-Shot-2019-03-26-at-11-42-32-AM.png](https://i.postimg.cc/6qs04MgT/Screen-Shot-2019-03-26-at-11-42-32-AM.png)](https://postimg.cc/zbjW9FJ1)

If we had to choose we would probably go either with Sentry or ELK stack using [APM](https://www.elastic.co/solutions/apm) on top of it. 

#### Save some $

AWS costs a lot, or at least a reasonable amount of money. We managed to save 10x $ by ordering a few GeForce GTX-1080 with 8Gb GPU on [Hetzner](https://www.hetzner.com/). Works really well for Europe. Also, I will not say it is super reliable, however if you put 2 clusters and a load balancer between them - everything works like a charm. Anyway, even 5 clusters would be 2x cheaper and 5 times more effective than AWS, sorry Jeff.

#### Being able to scale on request

If you ever tried helm in K8s or infrastructure as a code, you should know that scaling here is just a matter of a single line change. It's really easy to scale a pod once you are in K8s. Another question is how hard to get there, but we were lucky enough to get some expertise on a side.  

#### Having fun

Go is fun, is staticly typed, compiled language which was declared as a framework itself. Goroutines are more than famous, if you have any issues in production or life, just add a goroutine and it will get fixed. 

While we definitely had fun and go is indeed a well designed language it still caused us some pain. If you come from a 25 years old language which has tons and dozens of libraries it might seem that Go does not have many and its true. Need a database? Write plain SQL. Need an API server? Write your own middleware and request parser. Need a documentation? Luckily it's there. And of course not fully complete if we talk about swagger. 

On one hand, it saved us some time and pain produced by duck-typing, on another it is still not that mature and you need to give it back to the community even for 10 years aged language. Not so bad and not as sweet as people tell you. Take it with a grain of solt. Anyway, it worked well and we are sticking with it for small services.

#### Persistence 

The first milestone was Redis, because it is easy. You put json into memory and it lives well. Until.. you are out of RAM. Just drop more RAM on it. Correct. However if you have have processing RAM ends pretty fast. For us it was ~1Gb for 15 minutes of work. Let's increase it to 1 hour and you get 4Gb.
RAM is definetely a cheap resource, however do you know what is cheaper? Disk space and in 2k19 SSD is cheap as hell and as fast as rabbit. 

So we decided to go with a relational database, denormalize data and store it in PostgreSQL. Works pretty well and we are not going to move from it, unless we tired from data scheme maintenance and want to use NoSQL.
If we can do it the right way. We tried to use MongoDB as NoSQL database to store historical data, because you know what, every BigData startup should use MongoDB - in the end, we spent more time optimizing it and writing code to deal with unstructured data. Will give it one more shot next time. 


#### Extendable

Let's get to the picture again. 

[![Screen-Shot-2019-03-26-at-11-36-43-AM.png](https://i.postimg.cc/ZqrXqyC8/Screen-Shot-2019-03-26-at-11-36-43-AM.png)](https://postimg.cc/Y4Cdn0N0)

See containers on the right? Every ML task lives in it's own container and does only 1 thing. Either this is an index of euclidean distances or classification task or regression - we put it in the separate box. This allows us to scale bottleneck places at any time without a need to tweek the rest of the system. If classification is slow - add more classificator, too many API requests - add one more API server. Each box is responsible for only one thing and it does it really well.
If we decide to migrate from PyTorch to Keras or to TensorFlow - we can migrate a single container and see how it goes. If it works out - roll it out on the whole system. Does it sound like a plan?

#### SDK-friendly

As an engineer I really hate systems that work but either do not or have a very poor documentation. All you can do is to play with it, make a few guesses and who knows what is waiting for you when you go live. 
I integrated probably every 3d-party system that was launched before 2017 and amount of poorly documented services is astonishing. We want our users to have a pleasent experience and we work hard on it. 

A good starting point was a swagger documentation which we intend to use you autogenerate SDK-clients. This is basically what Amazon does and everything works well. However, every AWS client looks like you are writiing Java no matter what language you use. So the next milestone after we deliver well defined API would be native SDK-clients and ..drumroll.. examples on how to use them. Really, so many APIs lack at least examples, I am not even saying about documentation which might be misleading.


#### Summary

In the end, we are half-way on releasing new architecture and experimenting much more on what can be added as a feature. After we make sure it is feature-rich we are going to make it public, make it easy for developers and keep experimenting with scaling and performance tuning as it is fun.
Let me know if you want to know where we decided to go in API to make it extendable with new features.

