---
title: "How to Pytorch in Production"
date: 2019-02-18T17:52:04+02:00
draft: true
description: "
TensorFlow or PyTorch? What pokemon are you? 
We will talk today on top 10 mistakes engineers make using PyTorch in production.
"
---

ML is fun, ML is popular, ML is everywhere. Most of companies use either
TensorFlow or PyTorch. There are some oldfags who prefer caffe for instance.
Mostly it's all about Google vs Facebook battle.

Most of my experience goes to PyTorch, eventhough most of tutorials and online tutorials use TensofFlow (or hopefully bare numpy).
Currently, at Lalafo (AI-driven classified), we are having fun with PyTorch. No, really, we tried caffe, it is awesome, unless you have not spent a few days on installing it already.
Event better, PyTorch is 1.0 now, we used it from 0.3 and it was dead simple and robust. Ahhh.. maybe a few tweaks here, a few tweaks there.
Most of the issues were easy to fix and did not cause any problems for us. We stayed super happy about PyTorch.

Last year was fully backed by PyTorch and Python. The service changes and grows succesfully and we learn more and more about dynamic graphs and performance tweaks.
Here, I want to cover 10 most common mistakes for using PyTorch in production. Thinking about using CPU? Multithreading? Using more GPU memory? We've gone through it. Now let me guide you too.

### Mistake #1 - Storing dynamic graph during in the inference mode

If you have used TensorFlow back in the days, you are probably aware of the key difference between TF and PT - static and dynamic graphs.
It was extremely hard to debug TFlow due to rebuilding graph every time your model has changed. It took time, efforts and your hope away too.

Now, to make debugging easier dynamic graph is build and stored in so-called `Variables`. Every variable you use links to the previous variable buildiing relationships for back-propagation.

Here is how it looks in practice:
[![pytorch.gif](https://i.postimg.cc/4xjVQ04r/pytorch.gif)](https://postimg.cc/NK7KgpH4)

There is a lot of confusion between `eval` mode, `detach` and `no_grad` usages. Let me clarify it a bit.
After model is trained and deployed here are things you care about: Speed, Speed and CUDA Out of Memory exception.

To speed up pytorch model you need to switch it into `eval` mode. It notifies all layers to use batchnorm and dropout layers in inference mode (simply saying deactivation dropouts).
Now, there is a `detach` method which cuts variable from its computational graph. It's useful when you are building model from scratch but not very when you want to reuse State of Art mdoel.
A more global solution would be to wrap forward in `torch.no_grad` context which reduces memory consumptions by not storing graph links in results. It saves memory, simplifies computations thus - you get more speed and less memory used. Bingo!


### Mistake #2 - Not enabling cudnn optimization algorithms

There is a lot of boolean flags you can set in nn.Module, the one you must be aware stored in cudnn namespace.
To enable cudnn optimization use `cudnn.benchmark = True` and to make sure it cudnn does have permissions to look for optimized algorithms enable it by setting `cudnn.enabled = True`.

Please be aware your data must be on GPU. 

Another condition is to 


