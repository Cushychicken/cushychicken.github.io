---
layout: post
title: "How I Run User Interviews for Report Card Writer"
summary: "talk about it, talk about it"
tags: ['report-card-writer']
date: 2021-08-05

---

I’ve developed a pretty good playbook for the user interviews I run for Report Card Writer. My process is heavily inspired by Rob Fitzpatrick’s book *The Mom Test*, so if you’ve read that, nothing here will surprise you. If you haven’t read it - well, read this page *first*, and *then* go read Rob’s book. It’s the best book I’ve ever read on running user interviews!

Here’s the process I’ve come up with for testing my own theory about Report Card Writer, the boutique edtech software product I’m working on. 

# Before the Interview

Naturally, we’ve gotta start with some philosophical background. 

## Have a theory.

If you’re at or nearing the point where you're doing user interviews, you’ve already identified some kind of problem that your user base has. You might even have a thesis on how to solve that problem. Cool! 

The whole point of running a user interview is to confirm that: 

1. you’ve identified a real problem for your customers, 
2. your thesis maps to a solution for them, and 
3. their problem is meaningful enough that they’ll pay you to fix it.

Work as hard as you can to get your user to give concrete support of these three things. Things to look for: time consuming work, high annoyance, trying to make their own time-saving processes, etc. 

## Have an agenda. 

It’s a good idea to have a wireframe of how you’d like your user conversation to go. For me, this is just a bulleted list of your interview phases. 

My interview agenda for a Report Card Writer user interview looks like this:

- **Intro / chitchat:** Learn about your subject. Get them comfortable. 
- **Background questions:** Learn a little more about the specifics of the problem you’re trying to solve.
- **Demo + Feedback:** Show off the thing you’ve made to the subject. Get feedback on it. Encourage ruthlessness. 
- **Final Questions:** Ask for a sale. Ask for an introduction to someone else you can interview.  

Experience suggests that each of these interview phases average out to about 15 minutes per phase. I generally don't do anything to rigorously enforce that timetable. Getting good information is way more important than sticking to some arbitrary schedule. 

\## Write a questionnaire. 

Make a list of five or so questions that you want to answer over the course of your interview. Each question should have a very distinct, important purpose in the course of your conversation. 

I have about seven background questions I ask each teacher I interview. I’ve listed them below, along with some rationale behind the questioning. 

- **How do you write report card comments now?** This is a pretty open ended question that functions more for setup than anything else. It gets the subject primed to talk about their workflow, and frequently surfaces some touchpoint I did not know about (administrative review, student information systems, FERPA compliance, parent communications, etc). I find it’s generally the most time consuming question to answer, but it’s a good investment in the conversation. It builds rapport, helps reinforce my understanding, and allows your candidate to flex a bit of their mastery. 
- **Do you use a template?** This is a way of confirming that the problem of writing report card comments is sticky enough to merit investing in systems to make it go faster in the future. Most teachers say that they either have their own bank of comments to pull from, or reuse sentences written for other students again if they find them useful. That’s a sign to me that it takes a lot of time to do; investing in a template or a personal comment bank is confirmation that the process is painful, and that people are looking for shortcuts. 
- **How many students do you have to write comments for?** This question is further confirmation of the level of effort it takes a teacher to complete report card comments. Frequently, it’s enough information to answer the next question before I even ask it, so I might cut it out of the official template. 
- **How long does it take you?** This is where folks tie all of their introspection about their process to a tee-shirt-size of their time. I get wide ranges of answers here. Some teachers report as little as ten to fifteen hours on reports. Some teachers report completion over a month. There’s almost always an emotional component to this response. The most common are: 
  - Surprised Reflection: “Wow, I’ve never thought about how long it takes me to do these before."
  - Resigned Exhaustion: “Yeah, I have to give up a lot of nights and weekends to these reports."
- **Do you have a comment bank?** Further confirming what I know about investment by teachers into their own process. It’s also a double check as to whether my user is working in a public school, or a private school. Public schools tend to have a rudimentary comment bank built into their student information systems. They’re not great, but they’re all that many public school have, and without any sort of alternative infrastructure to give report card comments, it makes Report Card Writer less appealing to public school teachers. That’s my current working theory, at least. It seems to be true for most of the teachers I’ve interviewed, all of whom are overwhelmingly local to me. I’d love some feedback from outside the Massachusetts school districts.
- **Are you given your own dedicated computer at your school?** This is a quick technology survey question. It’s generally a proxy for “Can you use your computer as you like? Can you install your own software on it?” Reviews are mixed here. Answers here mostly guided me towards continuing to build Report Card Writer as desktop software. The more I learn, however, the more I’m coming to realize that a web app is almost certainly the right way to proceed, for a whole host of business reasons as well as technological ones. 
- **What kind of computer do you use for school? Mac, or Windows?** A quick technology survey question to understand what OSes I need to be compatible to. Currently checks out at a about 70% Mac, 30% Windows. I suspect many independent schools with limited IT support find it a net savings to spend more on Mac hardware and less on IT support. (You have to pay an IT person an annual salary. You only have to buy a MacBook every five years. That math checks out.) With regards to operating systems: I have never, ever, ever once been told “I use Linux” by a teacher. Never say “never”, but I have a strong inkling that Report Card Writer is never going to support Linux.

# Running The Interview 

OK! With prep work in hand, you can shift your focus to actually conducting the interview. This is not any more complicated than following the agenda you wrote up for yourself. Here are some of the  

## Ask more questions - especially if you hear something new. 

The most valuable moments in a user interview is *when your interviewee says something you’ve never heard before*. Opportunities like this are a gold mine for you. Since your goal is to uncover new insights as well as testing your hypotheses, you should take this opportunity to learn as much as you possibly can! 

Ask more questions! Admit your ignorance! Get your interviewee to say more! 

The questionnaire that you write in advance isn’t the end-all, be-all of your user interview. It’s a tool to jog your memory when you run out of things to talk about from the prior question. I’ve found that, if you’re really early in the customer interview process, it’s actually kind of hard to ask all of your pre-ordained questions. That’s because your interview subjects are positively bombarding you with useful information. 

## Give the demo! 

After about 15-20 minutes of conversation, the interview turns to showing off what I’ve made to the subject. This looks a lot like the demo video I put together for Report Card Writer:

https://youtu.be/qmr93fPzdaw 

Once I’ve given the shpiel, I lead with a simple question: “So - what d’you think?”

This *always* leads to a wealth of feedback from the subject. People tend to grasp what’s going on in the software very quickly. The conversation rapidly shifts to feature requests and excited talk of how they could apply it. 

A word about feature requests: any one of them, in isolation, is effectively useless. *Everyone* has a different workflow for creating report card comments. It’s only natural that they’ll ask your software to fit into *their* workflow. The point of user interviews, however, is to see what parts of the workflow are common to *everyone -* or, most everyone. Repeated requests for the same thing, from different people, are real. Those are things you should build into your software. Like I said in a prior post: every single user asked for Excel import/export functionality. This was an obvious feature to build in. I say “obvious” because unanimity is a hard thing to come by in any group of people. That kind of convergence in opinion is a dead giveaway for what you should be building next. It’s a tide you can’t really stand against. 

## Ask for the sale - and for introductions. 

I always end every interview with the same two questions:

- “I’m selling this software as it is now for $30. Would you like to buy a copy?"
- “I’m looking for more people to interview about this software. Can you introduce me to anyone you know that would be interested in providing their input?"

Like every question I ask, there’s a straightforward reason for each of these questions.

### Asking for the sale 

I want to get to a “Yes” if it’s possible to make a sale. I’ve had three people either Venmo me on the spot, or go directly to Gumroad to buy a copy after the interview. That’s cool, and really exciting! 

This has been the hardest part of the interview process for me to get used to. I’m an engineer by training. Asking for a sale is something I’ve always been deeply uncomfortable with. I suspect that more than a few readers fit that mold as well. It naturally comes with a fear of judgment. You’re putting your work in front of a customer. Are they willing to buy? What if they reject your work? It’s a very typical fear, and it only gets worse when the thing you’re pitching is something you’ve sunk a bunch of your own time and brain sweat into. 

The best advice I can give you on this front is to *get over it.* Asking for the sale is *the hardest part* of trying to build a product, but it’s also *the only part of the process that matters.* Getting people to pay you their hard earned money is the only way to confirm that your thing scratches their itch. 

Regardless of the outcome, the feedback is a gift. Don’t shy away from it!

### Asking for more leads

I want to find more people to showcase my work to. More perspectives and feedback is more learning! Since teachers work with many other teachers, they are a natural source of more contacts to mine. They also have the benefit of introducing me to teachers I don’t know. The farther outside my own network I get, the more people I meet who have no reason to spare my feelings about this little software product I’ve built. That’s good! Honest feedback about the product is the most I can ask for at the moment. 

# A Request

If you have read this far, I would absolutely love your help meeting more people to demonstrate Report Card Writer to. Do you know a teacher in a public, private, charter, religious, or other school? I would love it if you pointed them to [ReportCardWriter.com](http://ReportCardWriter.com), and mentioned that I’m looking for people to interview about the software. Don’t hesitate to reach out to me via this email address, or through [report.card.writer@gmail.com](mailto:report.card.writer@gmail.com). 

Thanks a bajillion!

