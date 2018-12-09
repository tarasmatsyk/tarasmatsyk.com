---
title: "How to Version PyTorch Models Better"
date: 2018-12-09T14:49:48+02:00
draft: false
description: "
Have you ever thought how to version pytorch models in production?
How would I know which version is running and how to store them correctly or better?
Torch.Hub and Git come to rescue
"
---

Every time new model arrives (because it is trained for a month or new ImageNet challange happens) we need to update our models in production.
The question "How do you I know which model is running at the moment?" appears over and over again.
As [PyTorch 1.0 came out recently](https://github.com/pytorch/pytorch/releases/tag/v1.0.0) there are a few features that I am personally a big fun of.
One of them is `torch.hub`.

So how did we store `~files` before?

### The old way
I will describe one option and will be looking forward to hearing the next paragraph as an answer.
If we have two files with different version, what do we do? I mean, not we, engineers, what would your mom do?
If she is more or less advanced users anyone would think of either `date.now` as a version control. Any photographers here?
When you do not care about data you can move right to the versioning: 

- v1/mydeepnet21
- v2/mydeepnet21
- v3/mydeepnet21

Ok, ok, I hear you. I expect everyone reading this post now screaming: "Your versioning shall not pass".
Anybody thinking of why people come up with git?

So what do I suggest? Actually, I like the idea PyTorch team come up with, I like how Docker manages images with tags and their docker hub.
Why do not we manage `mydeepnet21` in git too? 

### What's the use of git and PyTorch.Hub?

But models are too large to keep in git. Nobody stores files in git, it's designed for source code!!!11

Weeeelll, yes, but not exactly.

First of all, git is a version control system. And here is torch suggests us to version models.
Personally, I was thinking about this problem for a month from time to time and did not come with such a smart solution.


Let's assume you store models in a separate storage, maybe it is s3 or your isolated home-hosted old HDD. Does not matter,
all we need is a few MBs of memory and an ability to download this file through HTTP protocol.

Here is how model export look like:
```python
from torch import hub

hub_model = hub.load(
    'pytorch/vision:master', # repo_owner/repo_name:branch
    'resnet18', # entrypoint
    1234, # args for callable [not applicable to resnet]
    pretrained=True) # kwargs for callable
```

If you are about to use your own repo (and you are if you got here), just setup a repo with a `hubconf.py` file in it.
An example can be found in [pytorch/vision](https://github.com/pytorch/vision)

A content of `hubconf.py` is a declaration of your models with a link to downloadable file (model weights) in it. 
An above example would look like:
```python
import torch.utils.model_zoo as model_zoo

# Optional list of dependencies required by the package
dependencies = ['torch', 'math']


def resnet18(pretrained=False, *args, **kwargs):
    """
    Resnet18 model
    pretrained (bool): a recommended kwargs for all entrypoints
    args & kwargs are arguments for the function
    """
    from torchvision.models.resnet import resnet18 as _resnet18
    model = _resnet18(*args, **kwargs)
    # please notice file here. You can use settings.py or s3 path here
    # be as creative as possible, just make sure it's valid Python code
    checkpoint = 'https://download.pytorch.org/models/resnet18-5c106cde.pth'
    if pretrained:
        model.load_state_dict(model_zoo.load_url(checkpoint, progress=False))
    return model
```

Here is a full documentation on [torch.hub](https://pytorch.org/docs/stable/hub.html), personally, I recommend [examples and source code](https://github.com/pytorch/vision/blob/master/hubconf.py)

I think this is genius, as you can store your weights wherever you want and use a version control system to manage PyTorch models.

### So what's the big deel?

I do not about you, guys, but I am defitenly going to use this feature extensively as it is a very common struggle.
Git is my favorite tool in terms of version control and coming up with a small tweak to version PyTorch models is a big deal for me.
Yeah, you are right, any engineer who can write some Python and knows git could come up with a solution like that. Guess what, how many of them actually did?

Congratulation for everyone using PyTorch with the first major release. Happy to see it evolving.

