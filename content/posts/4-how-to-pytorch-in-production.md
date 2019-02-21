---
title: "How to Pytorch in Production"
date: 2019-02-18T17:52:04+02:00
draft: true
description: "
TensorFlow or PyTorch? What pokemon are you? 
We will talk today on top 5 mistakes engineers make using PyTorch in production.
"
---

ML is fun, ML is popular, ML is everywhere. Most of companies use either
TensorFlow or PyTorch. There are some oldfags who prefer caffe, for instance.
Mostly it's all about Google vs Facebook battle.

Most of my experience goes to PyTorch, eventhough most of tutorials and online tutorials use TensofFlow (or hopefully bare numpy).
Currently, at Lalafo (AI-driven classified), we are having fun with PyTorch. No, really, we tried caffe, it is awesome, unless you have not spent a few days on installing it already.
Even better, PyTorch is 1.0 now, we were using it from 0.3 and it was dead simple and robust. Ahhh.. maybe a few tweaks here, a few tweaks there.
Most of the issues were easy to fix and did not cause any problems for us. Walk in the park, really.

Here, I want to share most common 5 mistakes for using PyTorch in production. Thinking about using CPU? Multithreading? Using more GPU memory? We've gone through it. Now let me guide you too.

### Mistake #1 - Storing dynamic graph during in the inference mode

If you have used TensorFlow back in the days, you are probably aware of the key difference between TF and PT - static and dynamic graphs.
It was extremely hard to debug TFlow due to rebuilding graph every time your model has changed. It took time, efforts and your hope away too.
Of course, TensorFlow is better now. 

Overall, to make debugging easier ML frameworks use dynamic graphs which are related to so-called `Variables` in PyTorch. Every variable you use links to the previous variable buildiing relationships for back-propagation.

Here is how it looks in practice:
[![pytorch.gif](https://i.postimg.cc/4xjVQ04r/pytorch.gif)](https://postimg.cc/NK7KgpH4)

In most of cases you want to optimize all computations after model has been trained. If you look at the torch interface, there are a lot of options, especially in optimization.
A lot of confusion caused by `eval` mode, `detach` and `no_grad` methods. Let me clarify how they work.
After model is trained and deployed here are things you care about: Speed, Speed and CUDA Out of Memory exception.

To speed up pytorch model you need to switch it into `eval` mode. It notifies all layers to use batchnorm and dropout layers in inference mode (simply saying deactivation dropouts).
Now, there is a `detach` method which cuts variable from its computational graph. It's useful when you are building model from scratch but not very when you want to reuse State of Art mdoel.
A more global solution would be to wrap forward in `torch.no_grad` context which reduces memory consumptions by not storing graph links in results. It saves memory, simplifies computations thus - you get more speed and less memory used. Bingo!

### Mistake #2 - Not enabling cudnn optimization algorithms

There is a lot of boolean flags you can set in nn.Module, the one you must be aware stored in cudnn namespace.
To enable cudnn optimization use `cudnn.benchmark = True`. To make sure cudnn does look for optimal algorithms, enable it by setting `cudnn.enabled = True`.
NVIDIA does a lot of magic for you in terms of optimization which you could benefit from.

Please be aware your data must be on GPU and model input size should not vary. The more variaty in shape of data - the less optimizations can be done.
To normalize data you can pre-process images, for instance. Overall, be creative, but not too much.

### Mistake #3 - Re-using JIT-compilation
PyTorch provides an easy way to optimize and reuse your models from different languages (read Python-To-Cpp). You might be more creative and inject your model in other languages if you are brave enough (I am not, `CUDA: Out of memory` is my motto)

JIT-compilation allows to optimize computational graph if input does not change in shape. What it means is if you data does not vary too much (see Mistake #2) JIT is a way to go.
To be honest, it did not make a huge difference comparing to `no_grad` and `cudnn` mentioned above, but it might. This is only a first version and has huge potential. 

Please be aware that it does not work if you have `conditions` in your model which is a common case of RNNs.

Full documentation can be found on [pytorch.org/docs/stable/jit](https://pytorch.org/docs/stable/jit.html)

### Mistake #4 - Trying to scale using CPU instances
GPUs are expencive, as VMs in the cloud. Even if you check AWS one instance will cost you around 100$/day (the lowest price is 0.7$/h) Reference: [aws.amazon.com/ec2/pricing/on-demand/](https://aws.amazon.com/ec2/pricing/on-demand/). Another useful cheatsheet I use is [www.ec2instances.info](https://www.ec2instances.info/)
Every person who graduated from 3d grade could think: "Ok, what if I buy 5 CPU instances instead of 1 GPU".
Everyone who has tried to run NN model on CPU knows this is a dead end. Yes, you could optimize a model for CPU, however in the end it still will be slower than a GPU one. 
I strongly recommend to relax and forget about this idea, trust me.

### Mistake #5 - Processing vectors instead of matrices

- `cudnn` - check
- `no_grad` - check
- `GPU with correct version of CUDA` - check
- `JIT-compilation` - check

Everything is ready, what else can be done?

Now it's time to use a bit of math. If you remember how most of NN are trained using so-called Tensor(s). Tensor is an N-dimensional array or multi-linear geometric vectors mathematically speaking.
What you could do is to group inputs (if you have a luxury to) into tensors or matrix and feed it into your model. For instance, using an array of images as a matrix sent to PyTorch. Performance gain equals to number of objects passed simultaneously.

This is an obvious solution but few people actually using it as most of time objects are processed one by one and it might be a bit hard to setup such flow architecturally. Do not worry, you'll make it!

  

