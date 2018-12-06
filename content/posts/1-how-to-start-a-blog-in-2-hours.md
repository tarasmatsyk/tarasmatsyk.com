---
title: "How to kick off a blog in 2 hours"
date: 2018-11-22T22:07:52+02:00
draft: false
description: "
Have you ever thought how many hours you need to setup a blog?
What if I tell you it can be done under 2 hours? 
Hosting, comments, analytics, easy to deploy, should not be that hard, right?
Let's give it a try."
---

### So you need a blog

I know, I know, the very first question I receive when people hear "How to setup a blog" is:
"Why the hell you need to setup a blog if Facebook, Blogger, Wordpress, Medium and the next Big Thing exist?"

If this is the first question that comes to your mind - you probably do not need one.
The only reason to have your own blog is a desire to have a control over it.

<center><img src="https://i.postimg.cc/fTVvkKfX/darth-vader-PNG19.png"></center>

This is fine. Here are a few reasons for having a separate blog:

- adding contact me / about me / email subscription buttons
- being able to modify structure, theme, add any feature you want to (at least it's fun)
- being able to work on SEO, not all blogging engine provide you with a good search optimization for you username and, sorry, I a not sharing my google traffic
- you control advertising (or absence of it, ADs are the cancer of 21th century and everyone tries to avoid it)
- custom domain and direct links to only your content
- and tons of other reasons you can think of

### Approved, where do we start?

I will go from the easiest option to harder.
No need to get a bare metal or dedicated server from DigitalOcean, we are not enemies to ourselves.

In particular, here we will be talking about Static Site Generators, yes, this is one more post on SSG. 
During last 2 month I've done an analysis of most popular solutions, tried 3 of them and ended up with Go :party:


#### What are the options

My most favorite SSG are:

- [Jekyll](https://jekyllrb.com/) if you love Ruby or GitHub
- [Gatsby](https://www.gatsbyjs.org) if you like React.js
- [GoHugo](https://gohugo.io) if you want to give a shot to Go

There are other engines which might be a better choice for you, however my main factors were shipping time, speed and convenience of use. Disclaimer, I do not want to learn a new language just to keep a blog and all interactions with SSG have to be close to 0.

#### Jekyll

<center><img src="https://i.postimg.cc/05W8wk8K/logo-2x.png"></center>

Jekyll is awesome, because Ruby is awesome, because Matz is awesome and because [GitHub Pages](https://pages.github.com/) are outstanding. Some time ago you could not keep sources private and HTTP traffic secure, however its 2018 now, and GitHub improved greatly. 

Jay has tons of [themes](http://lmgtfy.com/?q=jekyll+themes) and it is extremely easy to setup in GitHub. Jekyll is definitely a way to go if you are looking for an SSG.

Now, let me explain what is the downside here. I like Ruby, really, it is an awesome duck-typing language which is outstanding for web prototyping and [DSLs](https://en.wikipedia.org/wiki/Domain-specific_language), however I do not like to manage environments too much. Since my main language is Python there are already conda, pip, pipenv and who knows what else lives on my machine. In my humble opinion SSG in python sucks and managing rvm or rbenv will not make my life easier. So, sorry, Jekyll, you are a nice guy but we have to break up, the problem was me.

#### Gatsby

<center><img src="https://i.postimg.cc/SKvBKsLm/gatsby.png"></center>

Oh Great Gatsby, let run a party? JS is a king these days, is in it? Ok, ok, do not throw tomatoes at me. Really, let's face the reality, JS has done huge progress in recent years and it is mainstream already for quite a while.

What if I tell you that you can run a blog in JavaScript? Meeh, tell me better what I cannot run in JS?
You are right, it is not so exciting anymore to write another thing in a prototype-based language.

As we are about to use a framework, an SSG framework, not that much a language itself, what are the pros of Gee?

Here is a list of companies who use it:
- Facebook (React docs)
- Nike (Just do it Campain)
- AirBnB
- Snapchat
- and who knows else

Btw, Gatsby [is officially a startup](https://twitter.com/gatsbyjs/status/999684072501792768) so you are using a well-funded product.

For me here are the pros of the framework:
- dozens of themes for the blog
- javascript and react-like experience (I like React and find it easy and readable to write a blog using one of the most popular frameworks)
- is under active development and adopted by big companies
- ease of use
- actively evolving

In my opinion, the adoption of Gatsby will only grow and the framework itself will become better and easier.
I am sure you will find more reasons to add it to your arsenal.

##### Cool, do I use it?

No. 

##### Wh#t? Why?

Gatsby is awesome, no doubts about it, but there are 2 things that stopped me. 
First of all, I still remember times when developing a website sucked and sucked a lot. 
Nowadays it is not an issue that much, however there is still a taste of sadness on the tips of my fingers when JS is used.
Sorry, you can evaluate `'3' + 2` and `'3' - 2` as an excuse. Yeah, yeah, the answer is pretty obvious, but I want it to be logical!! Not obvious!

The second reason is I am an engineer and there is always a brand new thing we all want to give a shot. Why not try something else? Ready?

#### GoHugo

<center><img src="https://i.postimg.cc/vBzcnqZW/gohugo.png"></center>

GoHugo is an SSG written in Go. Yes, this is one more post about Go. And Static Site Generators. And personal blogs. And all that stuff, you know.
It states that Mister H is the world's fastest framework markdown renderers which could be true, I have not tested by my own the performance of any of the previous options.
You are an engineer, how could you? I am sure all of them are good enough which is good for me.

##### Ok, so you wanted to write some Go?

Nah, I did not want to. An interaction with a framework and its language should be close to 0. I want to write some f##king markdown and get an HTML.
And on top of thaaaat.. I want a theme! Dozens of themes! 

So here is a list of advantages for me:

- Around [160 themes](https://themes.gohugo.io/)
- Easy to manage engine (get a go and do `brew install hugo`)
- Supported on most of SSG hostings
- Good documentation and community (Gatsby wasn't that good a year ago)
- actively evolving

From the comparison above it seems to be not that good as Gatsby, however I really like the way GoHugo is built, organized and managed so decided to give it a shot.
After a few months it ended up being.

#### I have an engine. What's next?

We've chosen an engine already, where to go next from here? 
If you decided to go with Jekyll - GitHub Pages is your ally here. 
For Gatsby and GoHugo my personal choice stopped at [Netlify](https://www.netlify.com/).
Even more, it was more of a battle of GitHub Pages VS Netlify and guess who won here?

If you have not used Netlify before here are steps you do to run a blog.

1. Put your blog under version control (`git init`)
2. Configure your remote repo in the Netlify (you can use GitHub, GitLab or Bitbucket. I tried all of them and stayed with GitHub just because I like green dots of commits)
3. Select an engine 
4. Add your domain
5. Boom! You are done.

#### Let's go through the process again.

I will be using GoHugo + Netlify in the example.

##### Step 1. Install Hugo

You would need to have `Git`, `Go` and `GoHugo` installed.
A full installation step can be found [here](https://gohugo.io/getting-started/installing/)

A manual process is next:

1. Go to the official Go website and install the language. [Guide](https://golang.org/doc/install)
2. You probably have Git installed already if you are reading this, but if not here is a [guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
3. Do 'brew install hugo' or `sudo apt-get install hugo` (Sorry windows, you have it's own path)

##### Step 2. Configure blog.

Here is a full guide on how to create a [blog](https://gohugo.io/getting-started/quick-start/)

1. I used the following command `hugo new site tarasmatsyk.com`
2. Go to the folder and initialize git `cd tarasmatsyk.com && git init`
3. Pick a theme [here](https://themes.gohugo.io/) and add it `git submodule add https://github.com/htr3n/hyde-hyde.git themes/hyde-hyde`
4. Create a new post `hugo new posts/how-to-run-a-blog-under-2-hours.md`
5. Add some content. Make sure you set `draft` to `false` before deploying it. You can test drafts in debug mode by running `hugo server -D`


##### Step 3. Deploy it.

1. Commit the changes to git. Create remote repo either in BitBucket, GitLab or GitHub.
2. Push all the changes to the repository of your choice. Mine is [here](https://github.com/tarasmatsyk/tarasmatsyk.com), for instance.
3. Make sure you have domain or accept the fact that Netlify will give you a random one.
4. Deploy config, domain, SSL, autodeploy are pretty easy and took me about 20 minutes. 
If you need a better guide on how to host Hugo on Netlify, here is a link to the official (documentation)[https://gohugo.io/hosting-and-deployment/hosting-on-netlify/]

There are other options than Netlify, however, I really like them and never ever had an issue with hosting.
As soon as you deployed a website, feel free to go the configured domain (or you a provided one) and enjoy the work done.

### Summary

This blog has been built using GoHugo and Netlify and it was damn easy. 
All of it, should take about an hour, maybe 2 if we include domain selection and purchase into the process.

Hope you enjoyed the reading and looking forward to seeing your blog in Hugo. Or Gatsby. Or Jekyll. Or your own framework of choice.

Now, we can start a holy war and answer the question of **Which framework do you use and why?**
