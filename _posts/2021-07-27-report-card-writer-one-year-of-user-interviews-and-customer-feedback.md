---
layout: post
title: "Report Card Writer: What I've Learned from One Year of User Interviews and Customer Feedback"
summary: "talking to your users is a hell of a drug"
tags: ['report-card-writer']
date: 2021-07-27

---

Almost exactly a year ago, I started a little boutique software company called [Report Card Writer](https://www.reportcardwriter.com?utm_source=cushychicken.github.io&utm_medium=blog-post-2021-07-27). As the name suggests, my flagship software product does pretty much one thing: it helps teachers write report card comments faster. Its core technical functionality is part survey form, part template-filler-inner, with a few extra bolt-on features that make it easy to create and populate the form and template, respectively. 

I've run about 20 user interviews over the course of the past year to show what I've made to real teachers, and to get feedback from them on what is useful. Here's what I've learned so far. 

# Process enhancements are the most useful thing the software offers.

One of my original worries about Report Card Writer was that the comments themselves, and not the software that assembles them, was actually the most valuable part of the system. 

That turns out to be diametrically opposed to reality. An interview I ran just days ago confirmed this almost to a T. To paraphrase the interview subject:

> "My department has a shared Google doc with hundreds, maybe thousands of comment samples. It's great inspiration, but it still takes me 20 hours to slice and dice all of that into useable report cards for my students." 

Teachers have no shortage of great ideas and insights about their students' behavior. Almost all teachers with a few years of experience have started to write comment databases for themselves to speed up the process of writing report cards. This teacher had all the raw materials ready to go to complete his report card comments. You’d think he could blast these out in an hour or two. Not so! The tools and processes for assembling these comments into a report simply are not there. 

Many teachers I've spoken with report having all the raw inputs they need, but lack a process to efficiently assemble these into a final output. My mental image is of a factory owner sitting in a factory without an assembly line. They are surrounded by nuts, bolts, and metal fittings. What they need is something that can fit all these pieces together into a useable product. 

I'm not selling nuts, bolts, or metal fittings. I'm selling the assembly line. The process, not the inputs, is the moneymaker. 

# Tiny UX features make big differences.

Things that you don't bother to think about, as a software engineer, make a huge difference to your users. 

A few examples:

* People forget what they wrote into their comment templates. A *lot.* Adding hover-over alt text on a radio button form resolved this immediately. 
* Adding "Shadow Text" to the report output area is a great way to suggest to users what will happen when they use the product. 
* People make mistakes - like selecting the wrong buttons - and need to clear them sometimes. Include a "Clear Selections" button! (More on that in a moment.)

Anything that reduces the friction of writing report card comments is a huge win for my customers. The less they have to think about it, the more use they can get from it. 

# Test - don't assume - what's right for your customers.

I had a lot of high-falutin' ideas about databases, backends, and "the right way to manage data" at the outset of creating Report Card Writer. I had a grand technical vision for making a product that was elegant and simultaneously useful to teachers everywhere. 

Every single teacher told me, bluntly: “This program should import and export *everything* in it to Excel. I live out of Excel.” 

I thought my customers needed one thing (a fancy database), when they actually needed something entirely different (importing and exporting from Excel sheets). I made a rough prototype of the software as a first pass, with *no* backend data system, showed it to teachers, and got some feedback. This saved me *hours and hours of work* on a product feature that *nobody but me* actually wanted. 

As an aside: Excel is still the king of *so much* in the world of data management. I'll wager that Excel is actually the world's most widely used backend database. (Probably the most underappreciated, too, in spite of a tidal wave of ink spilled to the contrary.) For most users, it’s great for data management! It’s easy to navigate visually, it accepts nearly any text input, and it’s easy to update and share. These are all huge perks for an audience that has no idea what SQL, programming, or databases are. 

I haven’t had a single question about databases asked to me. However, every person I have sat with during a customer interview has asked: "Can I import/export data from this software to Excel?” (I am not exaggerating when I say “every person”. Every single user in every single user interview has explicitly requested Excel or CSV file inputs and outputs.) In spite of a tidal wave of spilled ink bemoaning the fact, Excel is still the king of data management for many corners of the world. I'll wager that Excel is actually the world's most widely used backend database. (Probably the most underappreciated, too!) For most users, it’s great! It’s easy to navigate visually, it accepts nearly any input, and it’s easy to update and share. These are all huge perks for an audience that has no idea what SQL, programming, or databases are. 

# You can make a totally saleable product even if you forget really obvious features.

For most of last year, Report Card Writer didn't have a "Clear Feedback" button. The only way I found this out was getting an email from a user asking how to fix mistakes in the radio button form. If you’re the type that harbors perfectionist tendencies, this is one of those moments where you can feel your gut clinch with shame. 

Even in spite of this, I got a good chunk of really nice emails from teachers saying *what a huge help* the software was. That includes a note from the user who pointed out this oversight about the "Clear Feedback"! 

As a person who still *seriously* grapples with perfectionism, it was a revelation that *an imperfect product can still make you money.* Coming from a background in electrical engineering, I don’t think software people fully appreciate the advantage of delivering their work to customers via the internet. Pair this magical ability to fix your mistakes with a cohort of willing and enthusiastic users, and you have everything you need to improve your product constantly and expeditiously. 
This product has already given plenty of teachers hours and hours of their lives back, “Clear Feedback” button or no. Waiting until the “Clear Feedback” button was ready would not have helped them as much as it has by now. 

Ignore your perfectionism, and learn to just effin’ ship it.

# I'm not the only one attacking this problem.
I've unearthed at least two direct competitors and three adjacent competitors in this space over the past year just by talking to my customer base. That's great market validation! I've also gotten several pieces of feedback during user interviews that teachers are coming up with their own ingenious ways of solving this problem. Lots of them feature Excel, and `VLOOKUP()` tables. 
I started out thinking that I’d be the only one working on this. That was incorrect to the point of insanity. 

My focus, instead, has condensed to a question: how can Report Card Writer be the best option for teachers to write report card comments? 

I see a few ways to achieve that:

* Be the easiest solution to find,
* Be the easiest solution to use,
* Stick around, answer questions, and learn more from my customers. 

That’s it. That's my whole strategy. It's pretty boring. But a good strategy typically is. That's why execution is a differentiator. 

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I kind of think writing about engineering strategy is hard because good strategy is pretty boring, and it&#39;s kind of boring to write about. Also I think when people hear &quot;strategy&quot; they think &quot;innovation&quot;</p>&mdash; Camille Fournier (@skamille) <a href="https://twitter.com/skamille/status/1328763503973429250?ref_src=twsrc%5Etfw">November 17, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

# A Request

I would love, love, *love* it, if you have read this far, to tell a teacher you know about Report Card Writer. I would love it *even more* if you were willing to introduce me to that teacher. I have so much more to learn, and so much more feedback to gather, that any route to meeting those people is a huge help to me.  