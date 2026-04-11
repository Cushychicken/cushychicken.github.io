---
title: "Things I Wish I'd Done Differently Building a Niche Job Board"
summary: ""
tags: ["programming"]
date: 2022-05-25
slug: "done-differently-rtl-jobs"
aliases: ["/things-i-wish-i-d-done-differently-building-a-niche-job-board/"]
---

I’m closing in on a year - a whole dang _year!!_ - of working on [RTLjobs.com](www.rtljobs.com). It’s a job board for FPGA and RTL engineers. (If you’re an FPGA engineer, and looking for a new gig, I’d love it if you signed up for [our mailing list](https://www.rtljobs.com/signup).)

I’ve learned a whole lot about building and shipping webapps that I didn’t know a year ago. Knowing what I know now, here’s what I wish I’d done differently at the outset of this project.

# Bought a Website Theme

Making pretty websites is _hard_. Really, _really_ hard. Just making the website work at all was hard, but making it look good is a wholly separate class of hard. 

The first pass at a user frontend was pretty ugly, but it worked.

The second pass was pretty ugly, and also a bit user hostile. It’s also still live on the site right now. To paraphrase Mitch Hedberg: it used to be ugly. It still is ugly, but it used to be, too.

The third pass at a UI… is a template that I bought.

Why?

* It’s got a lovely color scheme, and pleasing shapes that catch the eye.
* It would have taken hundreds if not thousands of hours for me to make this myself.
* It was twenty four dollars to purchase.

You know why I know it was a good decision? My wife saw me working on it over my shoulder and said “Oooh, that looks good - did you make that?” My wife, god bless her, has never before extemporaneously commented on software I was working on. The fact that she did spoke volumes to me. That pretty template was able to connect with her in a way that none of my shitty, hand built UIs could.

That connection, plus the countless hours it saved me, likely make those $24 the best time-for-money exchange I have made so far since starting this project. I'm _really_ excited to see what impact it has when I roll it out in a few weeks.

# Used an ORM

For almost the entirety of its life, the database transactions for RTLjobs have been executed by Python as a thin wrapper around handwritten SQL queries. If you know anything about web apps, you’re probably asking “Why would you do that, when you could use an ORM, like SQLalchemy?”

Good question. I don’t have a particularly good answer, except “I didn’t really know what I was doing at the start, so I picked something I could reason about and debug."

I’ve never had good luck using ORMs before now. Every time I’ve tried using one, I’ve been faced with opaque error messages. It was a huge advantage to me at the start of this project to be able to open a SQL console, run a query, confirm expected results, and then duplicate it in Python by copying from SQL console to Python source file.

I hadn’t had that experience with ORMs the last few times I’ve used them. That might just be stale info on my part. The last time I really putzed with SQLalchemy was probably 2014. A few things have changed since then, all of which are strongly in my favor:
* SQLAlchemy has been under active development, and hardened by a number of production deployments,
* StackOverflow has gotten steadily more populated with high quality answers to common SQLite and SQLAlchemy questions, and
* I got better at writing both Python and SQL.

I’m definitely aware that I chose a suboptimal solution for the first pass. It was crappy in a lot of ways, but I at least knew how to debug it. I think of it as "Kernighan’s Law in action". If you’re not familiar, Kernighan claimed: "Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.” I was definitely not smart enough or experienced enough to debug SQLAlchemy errors a decade ago, or even a year ago. The constant practice of working on the app, day in and day out, has slowly fixed both of those problems. My internal cache got slowly populated with working patterns, and a debugging workflow that I could leverage to fix problems.

A meta question that I frequently contemplate on this subject is: “Would this project have happened at all if I’d used SQLalchemy from the start?” It’s a good question. I suspect the answer is “No”. Adding more things to learn about at the outset would have been a hindrance, not a help. Now that I have a better idea of what I’m doing, SQLalchemy is a force multiplier, rather than another thing I have to figure out how to use.

# Used Django

My brother in law works for a big financial data company in NYC whose stack is mostly Python. I had a conversation with him about launching this last summer that went something like this:

> Me: _“I’m thinking of turning my job site into a web app. I want to use Python because I know it best. Should I use Django, or Flask?”_
>
> BIL: _“Django. It does a bunch of web app stuff like admin and logins automatically for you.”_
>
> Me: _“OK, cool, I’m gonna use Flask because I know it better. Kthxbye."_

Charlie, I’ll say this publicly here: you were totally right.

I finally bit the bullet and started writing all the classic SaaS utilities a web app needs: user logins, admin consoles, analytics pages, etc. This is an ongoing exercise, and the report I’ll give from the trenches is:

* writing your own admin utilities sucks
* writing or integrating a login scheme sucks

Don’t get me wrong. Flask-Admin is fine. Flask-Login is fine. Both of these are a lot easier to deal with now that I have a proper SQLalchemy backend to plug in new modules with. (Hooray for db model wrapper classes!) But Django is “batteries included” for both of these functions. With, like, a decade plus of production heft behind it. I’d have saved myself this big rewrite if I’d chosen Django from the start. Not to mention that I’d have invested the intervening months into learning Django better, rather than learning Flask.

I recall a Google Chat I had with another solo job board runner a few months ago. He was showing me some of the backend features of the site. I was floored by it.

> Me: _“How long did it take you to write this nice looking admin panel? I love that you can go in and edit all the posting content so easily.”_
>
> Him: _“I didn’t write it. It’s the Django default. All I had to do was add my db models to it, and the views took care of themselves.”_
>
> Me: _“….."_

So, given all that - why’d I pick Flask?
* I knew it better.
* I knew I could get the site working with Flask.
* I’ve tried to do the Django tutorial at least four times in the last ten years, and fell off the tutorial wagon every time.

Back to that meta question again: would I have shipped this if not for Flask? Maybe, maybe not. I lump it into the same general consideration as SQLalchemy. (Plus a whole host of other tech stack stuff: Postgresql, single page applications, Redis, yada yada yada….)

# Store my database somewhere other than Git

My current biggest technical problem is that the size of my Git repo is exploding. It's currently weighing in at about 3GB. This is really only a problem because I don’t have a ton of hard disk space with my cloud provider, and I’m too cheap to pay for more. (Hi PythonAnywhere! I love you!)

Digging into why revealed that git is storing multiple objects that are similar in size to my production db (which is about 90MB). Turns out binary files, like SQLite dbs, are not great candidates for git storage.

I have a few unanswered questions stemming off of this:
* How will I back up my db if not for git?
* How will I get the .git subfolder back under gigabyte levels?

In either case, I have more learning to do. (I got some great ideas from [this blog post](https://litestream.io/alternatives/cron/) by the folks at Litestream.) But I wish I’d spared myself the ~~agony~~ learning curve of trying to figure out how to slim down a git repository.

# Added a blog into the site from the very start

The biggest problem my site has isn’t technical. It’s marketing. You’d have a real hard time convincing me that’s not the biggest problem for a job board. (Or just about any web business, really.) Tech wise, all of the things I’m struggling with are solved problems. This is a CRUD app with scheduling, not general conversational AI.

After reading every scrap of writing by Patrick McKenzie and Peter Askew, I’m firmly of the belief that organic SEO is the only sustainable way to grow the company. Consequently, I’m all in on some pretty bread-and-butter SEO strategies, mainly:
* Owning a boatload of great domain names for this job space, all of which reroute to my site
* Structuring my URLs so that they’re google and human readable (you already know what you’ll find at `https://www.rtljobs.com/internships`, for example.)
* Writing interesting content that’s relevant to the people I want to serve

That’s a hard, but not impossible task. I have about a decade of EE experience, which lets me speak confidently to my market. I also have modicum of ability as a writer. I ain’t Hemingway, but I can get a point across using words. (I mean, you made it this far, right? 😄) I’ve already had some success with this. The first blog post I was able to write based on my experience running the site hit the front page of HN. (Maybe even the top - I don’t remember exactly. Suffice it to say that it was a big deal for one day on the internet.) That was posted on my personal blog. Tricklethrough traffic to RTLjobs from that post has been great, even months later.

However, I keep wishing I’d blogged directly on RTLjobs.com. Not doing so, I think, has been a missed opportunity for my SEO-ness. I can’t help but wonder what the SEO to RTLjobs would be like if I had just taken the time to set up a blog for RTLjobs, and point people to that. All the link juice my personal blog is getting could be going to RTLjobs instead.

The effort to set up a blog has been slightly higher than what I can accommodate with my time. Once I complete this SQLAlchemy integration, getting a Flask-Blogging integration rolling is my next to do.

# Choices I Don’t Regret

Part of what spurred me to write this post was consuming years of prescriptive advice from HN about “how to build a web app”. In hindsight: almost all of this advice was not meant for me. It was for people building web apps at huge scale. (Come to think of it, I wonder if most of that stuff wasn’t really just content marketing for SaaS startups.)

Here are some of the things I don’t regret doing.
* Using SQLite as my production database. A good day for me is a hundred users. The statistical odds of dealing with concurrency or any of other “big league” type database issues are far, far in the future for me, if I ever get there at all. I want RTLjobs to be very niche and tightly focused; I don’t mind leaving the mainstream to Indeed, Monster, and Google Jobs. For the scale I’m operating at, SQLite is battle tested, well documented, and has plenty of good StackOverflow answers. I’ll take that over MySQL or Postgresql, which are a bit harder to provision and set up.
* Not building a tight Stripe integration right at the get go. You can get a long way to validating your idea and your market with email and Stripe payment links. You just need some way to accept payment from the very get go. If you’re not, you’re denying yourself the true test: whether people cough up money to use your service.
* Choosing PythonAnywhere as my site host. Cannot possibly stress how much I love them as a web host. The pluses I can think of offhand: transparent pricing, lots of handholding tutorials, reasonably comprehensive forum help for common web app issues. All of these are huge for people like me, who are mostly ignorant of professional web app development and hosting, except for what I’ve managed to learn on the fly.
* Paying an accountant for an hour of tax/accounting advice on structuring the business.
* Automating post approval. At the start, I reviewed each post manually. That's hard to keep up with when your crawler is hauling in several hundred new jobs a day. I’m probably not catching every job that could qualify for RTLjobs, but I’m confident I’m catching more than 80% of the ones that do.
* Buying bitly for clickthru analytics. I spec’ed out building my own link clickthrough platform, estimated it’d take a month of full time effort, then did some searching for alternatives. Bitly offered everything I’d build, and more, at the low low cost of $350 a year. I think I gave my credit card a friction burn from the speed at which I extracted it from my wallet. It’s a bit expensive for what it is, and I might choose to find a cheaper alternative closer to the end of the year when my annual subscription expires, but the time savings alone were well worth it. The lesson here is clear: buy more services. They are worth the investment vis a vis the time cost it’d take you to do it yourself.
* Talking to users, via Reddit, HN, or email. I try to comment to every single person who engages with me and my site - even the snarky ones, or the ones complaining about what it doesn’t do. Why? Because you never know what might happen. I wrote back asking for more details from the one person who openly flamed my UI on Reddit, and got a really thoughtful reply. Fast forward six months, and I managed to place him at his first job as an FPGA engineer. You can’t make this shit up. Talking to users works. Especially disgruntled ones. They’re upset enough to be honest!
* Not referring to myself as "an entrepreneur". I much prefer "small web business owner". More of a mouthful, but it says a lot more about my goals.
