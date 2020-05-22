---
layout: post
title: Baby Steps Part II
---

Previous parts: [Part I](https://akondas.com/Baby-Steps-Part-I/)

Last time we've talked about how to start contributing to Open Source - and to reinforce my statement, I've started contributing to a relatively new library for supporting Chrome headless browser called [Rod](https://github.com/ysmood/rod) and came back to my contributions to [Go-Audio](https://github.com/go-audio).

Today we will talk about next thing we should think about in our `baby steps` approach:

## Build Everything

I will divide this post into three parts basically:

* [How I Did It](#did)
* [How I Would Do It Again](#would)
* [Conclusions](#conclusions)

We'll tackle how I've learned Go using this principle, how I'd follow it learning something new and how you can use the same lessons for anything else. Let's Go!*

> I definitely need to work on my Go puns...

<a name="did"/>

## How I Did It

My story with Go, as I've told it [here](https://www.youtube.com/watch?v=O7tyWi24mMY) was simple - my good buddy Alex (hope you're reading this mate!), with whom I was working in a start up in London told me:

> DUDE, THERE'S AN AMAZING LANGUAGE YOU NEED TO LEARN. IT'S CALLED GO AND YOU WILL LOVE IT!

Given that Alex knows his stuff EXTREMELY well, I was more than keen on jumping on this train. My approach was simple:

* [Research](#research)
* [Apply](#apply)
* [Improve](#improve)
* [Push](#push)
* [Repeat](#repeat)

<a name="research"/>

### Research

I needed to know what Go is about. I didn't spent much time reading about the syntax, or learning who build it and why, I just wanted to start writing in it.

So, I've started like everyone with [A Tour of Go](https://tour.golang.org). Did all the lessons, wrote down the very first syntax rules and keywords. But I needed more.

<a name="apply"/>

### Apply

First thing I've written with Go was a simple, `CSV` based [API](https://github.com/youshy/go-guitar-api) for keeping a track of my guitars.

The code there is ugly, not well written - but it works. As a proof-of-concept, that's good enough for me.

<a name="improve"/>

### Improve

Yet after a few days, that wasn't what I've wanted. Back when I've started working with Go, I was a Full Stack Software Engineer, working mostly with Node and Vue. You know the feeling when you kinda know how something works and you're just anxious to work it out in the new field? 

That's how I've felt! So, my next thing was more production-ready [API](https://github.com/youshy/go-json-api) that, finally, works with `JSON` files. Looking at it now I don't like a lot of stuff there: the router is not how I'd write it now, handlers are messy and it still doesn't have any database connection ready.

<a name="push"/>

### Push

Jump almost a year from this moment, I'm a full time Go developer, writing a ton of RESTful APIs and my latest approach to how I do it is [here](https://github.com/youshy/Hands-On-Ready-To-Deploy-Golang-CRUD-API) - also, if you want to follow me on that journey, join me in the next [DevOps Playground](https://www.meetup.com/DevOpsPlayground/events/270672982/) from the comfy corner of your couch!

There's still a lot of things missing from this tutorial about how I do stuff - which I'll probably talk about later on.

<a name="repeat"/>

### Repeat

In those four steps, I took a very beginning of my journey from almost a zero to somewhat being happy with what I write.

But that's just a tip of the iceberg - since I've started learning Go I've written:

* [A utility tool for switching between multiple github accounts and creating repos remotely](https://github.com/youshy/gitutil)
* [A CLI for checking Wikipedia without leaving my terminal](https://github.com/youshy/wiki)
* [A small study on using PostgreSQL triggers with Go](https://github.com/youshy/go-psql-triggers)
* [A pacman in terminal](https://github.com/youshy/pac-go-man)
* [A wrapper for python files which automatically creates an API](https://github.com/youshy/goapify)
* [A helper package for writing these posts](https://github.com/youshy/nju)
* [A checker for my two favourite mills if they have opened their online shop](https://github.com/youshy/bready-steady-go)

And many more. I've been playing with [processing audio with Go](https://github.com/youshy/biip-beep-boop) and [gRPC based APIs](https://github.com/youshy/mongo-over-grpc) recently - and that's just in my free time.

Work-wise I have another set of things I'm working on.

---

<a name="would"/>

## How I Would Do It Again

Following the same five steps, let's think about how I would start learning... C++. It's been a long-time personal goal for me to be fluent in C++ but since Go has rocked my world, I haven't had that much time to work on it. 

How I'd approach that - for the sake of this part, let's assume I'll write a VST (Virtual Studio Technology) plugin for recording.

### Research

The simplest way would be to build a two-knob, very simple UI plugin with a distortion circuit - something that takes the initial signal, cranks up the gain and returns the overdriven sound. For now, I wouldn't worry about the sound being somewhat digital (as it would be).

### Apply

As we know the UI is not that great, I'd probably spend some time in honing my skills in that domain. Given that I'm not the best designer ever - I can hear things well, I don't see the design well - that would take some time.

### Improve

Once I'd get the UI bit a tad better, then the next logical step would be to manipulate the equalization in my new plugin. So that'd require extra step in programming this part.

### Push

Once done, I'd move to another VST - maybe this time a tape simulator? Full blown delay circuit? Reverb? Impulse Response -based reverb?

### Repeat

Of course, while doing that, I'd probably build an in-terminal Tank game or an RESTful API (as far as I know, that's not the easiest task in C++) or literally anything else.

---

<a name="conclusions"/>

## Conclusions

The key to this approach is to repeat, repeat, repeat. Don't get sad about something not working the way you want it to work and don't overthink it. [Just do it!](https://www.youtube.com/watch?v=ZXsQAXx_ao0)

Considering how much time has passed since I've written a lot of these small apps:

* Am I happy about the quality? Not that much.
* Am I proud of them? Heck yes!

Even if they are not the best, they show where I've been some time ago. These are a clear indicators of the progress I've made to be where I am now.
Although sometimes I end up wandering in "what-could-have-been" part of my memories, I wouldn't change a thing I've did in my life - all of these things brought me here - and I'm more than happy about it!

I've used the same approach in music, baking, languages - heck, even in circuits building. It applies to anything new you want to learn - and it works.

Give it a try and then @ me on [Twitter](https://twitter.com/arturkondas) and let's chat!
