---
layout: post
title: "The ease of picking up web development is greatly exaggerated"
summary: "it errored out again pls help"
tags: ["programming"]
date: 2023-06-19
---

I quit my job in July 2021, and started writing a webapp for something to do. (Are you an FPGA engineer, and looking for a new job? Check us out at www.FPGAjobs.com.) I had assumed, as a credentialed engineer and longtime HN lurker, that picking up web development was going to be pretty easy. I was absolutely wrong. Here's why.

# Multiple Languages

You need multiple programming languages to get anything done in web development. You need some understanding of HTML, CSS, and Javascript to make a pretty, functional web page on the frontend. You also need some handle on SQL, plus one other scripting language to store all of that on the backend. (I chose Python, because it was the one I knew the best.) You will probably also need some idea of Linux OS command line utilities and shell scripting to automate all the grunge work that comes with maintaining a server.

For those of you not keeping count, that’s six programming languages. Six.

Before this, the bulk of my programming experience has been writing firmware for embedded systems. This has given me a lot of appreciation for the fact that almost all firmware is written in C. C is the lingua franca of embedded systems. OK, yeah, you get the odd aerospace weirdo who's obsessed with Ada, the occasional C++ dabbler, and the Rust Evangelism Strike Force, but these are edge cases, not norms. There are plenty of ways in which this is a curse: you have to worry about things like pointers, and memory management, and not having a wealth of rich syntax. The blessing is that, when it comes to the programming languages available to you, there’s really only one tool for the job.

I had a hacker’s knowledge of Python and a teensy bit of shell scripting experience when I started writing FPGAjobs. I’ve had to learn HTML, CSS, Javascript, and SQL pretty much from scratch to get this project off the ground. The corresponding cognitive load is boggling. You have to do so much more work and pay so much more attention to get a job done. “Am I remembering the right syntax? Am I working form the right set of rules here?” In firmware, this is easy: it’s all just C.

I think, often, about how I’ve had a lot of major advantages working in my favor here. The main thing I can think of is going to school for electrical engineering. I had a pretty good amount of exposure to programming through my university curriculum. Even with that exposure, getting the hang of web development was a struggle - and still is now, on many if not most days.

And yet, we still live in a world where coding bootcamps promise to get people ready to be professional software engineers in three months. Three months. It really is comparable to those ads that tell you "Learn Spanish in one week!" Nobody who really knows how to speak Spanish believes it, because it just ain't so.

# Hard to Know Where to Solve Your Problems

Another two-edged sword of web development is having so many options of where to solve your problems. All of those languages you have at your disposal are good at so many different things. They present so many different tools for the job, and I frequently find it really daunting to figure out what to use where. Should I do this server side, in Python? Or client side, in Javascript? Should I rewrite this as a SPA? What about using the HTML5 implementation of dropdowns, instead of a Bootstrap package?

Questions like this overwhelm me all the time. It's hard - maybe impossible - to know if I'm using the best tool for the job. That, in turn, frequently stymies me in forward progress. I have so little time to commit to forward development these days, that I generally don't do much of anything unless I know I can do it quickly. The bright side of this is that it incentivizes me to do a lot of upfront research before actually committing code. This is much easier to do than sitting at a keyboard trying to bang out a feature. I can just open up a crapload of tabs on my phone, read, and email myself the ones I think will work later, when I have confidence in the right approach.

I suspect that high abstraction has something to do with this. It's pretty difficult to figure out the interfaces in your software systems when you're just starting out writing webapps. The tutorial ecosystem is not particularly helpful in this regard. Plenty of web tutorials start off by throwing you into the deep end, with tools like SQLalchemy or React, where you don't have a ton of insight into their underlying value proposition. To make matters worse, most of these tutorial articles seem to be written by some combination of: novices, boot camp participants, or SEO farmers from India or the Middle East. Most of them don't have any more insight than you do about the functioning of the high-abstraction tools they're using. They just know a method of solving a problem that works - not why, or how, which is problematic when you need to get down in it and fix something.

In a way, not having the abstraction when starting out taught me a lot about why high abstraction systems were a good thing. Case in point: I didn't use an ORM when I started writing FPGAjobs. Instead, I hand wrote all of my SQL statements in a SQL terminal, ensured they worked, then copied them into a quoted block inside a Python wrapper. Glamorous? No. Elegant? Certainly not. Functional? Without a doubt. And, I could debug it! Not so, my first few forays into SQLalchemy. (I never have gotten SQLalchemy to work. I ended up choosing Django in part for its built-in ORM. Very happy with it!)

# Hard to Ask Questions

You have to be a certain depth into any field to know enough to ask good questions. This is partly terminology: it's hard to convey your meaning concisely to others when you don't have shared vocabulary to discuss it. (Aside: lack of shared vocabulary for description makes the discovery of calculus or other advanced mathematics that much more remarkable, in my opinion.) It's also hard to know "what you should know" in advance of asking. Nobody likes asking a question on a forum and getting "RTFM" back as their answer.

Granted, it's easier than ever to get answers - StackOverflow is a wealth of programming knowledge unlike any before it known to man. But it can be hard to dig for gold without knowing what gold looks like, and the nuggets you seek on StackOverflow are generally knowledge. How do I know what I'm looking for? How do I know the shape of my problem well enough to ask for a solution? Especially if I don't have an error message to paste.

# Not Enough Practice

I went to school for electrical engineering. I got a reasonable amount of exposure to programming through that. I managed to leverage that skillset into several more years of experience in bare metal embedded development. In that regard, I am pretty accomplished: I've written firmware that runs on several million consumer devices the world over. When it comes to Python and the like, my reputation starts to evaporate. I’ve used Python and shell for all sorts of miscellaneous scripting tasks throughout the years. I’ve written one-off scripts, some simple desktop GUIs, and some pretty complicated Jupyter analysis packages for signal processing stuff. Never anything as coherent or as large as a web app.

Even with all of that experience, I wouldn’t say I was good at programming when I got started on the project. I could typically accomplish what I needed to do, but I wouldn’t rate myself as “comfortable” until pretty far along in the project. After about a year of spending two to eight hours a day on FPGAjobs, I would have rated myself as a “competent but fairly junior full stack developer”.  I wouldn't say I've gotten meaningfully better at development since then, as I've taken a full time job, and don't have the kind of time to dedicate to the project that I used to.

# The Real Hard Parts

I consume enough HN schlock that I have seen the phrase "managing complexity" until I'm green in the face, but I do think that's one of the hardest parts of webapp development and software engineering. Software is complicated. Django is complicated. It's structured, but it's a pretty deep and nested structure. You need a bit of familiarity with it before you can work effectively with it, and that takes time to develop.

But what I think we're really saying is that: the less complex something is, the less about it we need to keep in our heads. Thus, managing complexity is really an exercise in managing mental models. This, I feel, is a skill that most folks don't work hard enough to develop. Not nearly enough people use pen and paper as development tools. For my workflow, they're essential. They free me of the overhead of having to remember my design. That way, I can think critically about what I'm doing, without expending effort just remembering what I'm working on. It's sort of like the zen notion of the actor and the critic: if you're just critiquing, you can't act. If you're just acting, you can't critique.

