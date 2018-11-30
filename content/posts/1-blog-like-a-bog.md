---
title: "How to kick off a blog in 2 hours"
date: 2018-11-22T22:07:52+02:00
draft: true
description: "
Have you ever thought how many hours you need to setup a blog?
What if I tell you it can be done under 2 hours? 
Hosting, comments, analytics, easy to deploy, should not be that hard, right?
Let's give it a try."
---

## So you need a blog

I know, I know, the very first question I receive when people hear "How to setup a blog" is:
"Why the hell you need to setup a blog if Facebook, Blogger, Wordpress, Medium and the next Big Thing exist?"

If this is the first question that comes to you mind - you probably do not need one.
The only reason to have your own blog is a desire to have a control over it.

<center><img src="https://i.postimg.cc/fTVvkKfX/darth-vader-PNG19.png"></center>

This is fine. Here are a few reasons for having a separate blog:

- adding contact me / about me / email subscription buttons
- being able to modify structure, theme, add any feature you want to (at least it's fun)
- being able to work on SEO, not all blogging engine provide you with a good search optimization for you user name and, sorry, I a not sharing my google traffic
- you control advertising (or absence of it, ADs are are cancer of 21th century and everyone tries to avoid it)
- custom domain and direct links to only your content
- and tons of other reasons you can think of

## Approved, where do we start?

I will go from the easiest option to the less easier. No need to get a bare metal or dedicated server from DigitalOcean, we are not enemy to ourselves.

In particular here we will be talking about Static Site Generators, yes, this is one more post on SSG. 
During last 2 month I've done an analysys of most popular solutions, tried 3 of them and ended up with Go :party:


#### What are the options

My most favourite SSG are:

- [Jekyll](https://jekyllrb.com/) if you love Ruby or GitHub
- [Gatsby](https://www.gatsbyjs.org) if you like React.js
- [GoHugo](https://gohugo.io) if you want to give a shot to Go

There are other engines which might be a better choice for you, however my main factors were shipping time, speed and convinience of use. Disclaimer, I do not learn new language just to keep a blog and all interactions with SSG have to be close to 0.

#### Jekyll

<center><img src="https://i.postimg.cc/05W8wk8K/logo-2x.png"></center>

Jekyll is awesome, because Ruby is awesome, because Matz is awesome and because [GitHub Pages](https://pages.github.com/) are outstanding. Some time ago you could not keep it private and secure, however its 2018 now, GitHub improved greatly. 

Jay has tons of [themes](http://lmgtfy.com/?q=jekyll+themes) and it is extremely easy to setup in GitHub. Jekyll is definetelly a way to go if you are looking for a SSG.

Now, let me explain what is the downside here. I like Ruby, really, it is an awesome duck-typing language which is outstanding for web prototyping and [DSLs](https://en.wikipedia.org/wiki/Domain-specific_language), however I do not like manage environments too much. Since my main language is Python there are already conda, pip, pipenv and who knows what else lives on my machine. In my humble opinion SSG in python sucks and managing rvm or rbenv will not make my life easier. So, sorry, Jekyll, you are a nice guy but we have to break up, the problem was me.

#### Gatsby

<center><img src="https://i.postimg.cc/SKvBKsLm/gatsby.png"></center>

Oh Great Gatsby, let run a party? JS is a king these days, is in it? Ok, ok, do not throw tomatos at me. Really, let's face the reality, JS has done huge progress recent years and it is mainstream already for quite a while.

What if I tell you that you can run a blog in JavaScript?
