---
bg: "velociraptor_solo.jpg"
layout: post
title: "Software = Agile! Hardware = Waterfall!"
summary: "clever girl"
tags: ['working-on-hardware']
date: 2020-10-09
---

There's a fundamental tension between software orgs and hardware orgs, and I think I've spotted it. 

|Task               |Software    |Hardware          |
|-------------------|------------|------------------|
|Idea Capture       |Write code  |Schematic Capture |
|Engineering Checks |Code Review |Layout            |
|Operations Handoff |Check In    |Design Release    |
|Validation         |QA Testbeds |Electrical Test   |
|Ship!              |Ship it!    |Ship it!          |

The tasks here don't align perfectly, but they're reasonably analogous to one another. Do you see the difference?

If not, it's due to a dirty trick on my part. The difference is not obvious here, because I omitted it: 

|Task               |Software    |Hardware          |
|-------------------|------------|------------------|
|Idea Capture       |Write code  |Schematic Capture |
|Engineering Checks |Code Review |Layout            |
|Operations Handoff |Check In    |Design Release    |
|Validation         |QA Testbeds |Electrical Test   |
|Ship!              |Ship it!    |Ship it!          |
|Ship!              |Ship it!    |Ship it!          |
|**Total Time**     |**2 weeks** |**2 months**      |

Hardware has a much looser feedback loop than software organizations. This is largely due to the shift in technology that has allowed software orgs to deploy their work much more rapidly and at much grander scale than hardware orgs. (Thanks, internet!) 

As a result, "Product Management" has basically evolved to mean different things for each specialty.

- **Software:** Figure out small bets that improve your consumer or enterprise software offering; try them out, ship them, and iterate as results improve. 
- **Hardware:** Figure out what the customer needs, figure out a plan to get there, and then hold the line on what constitutes "the right course" as execution takes place.  

Note here that the goal here is exactly the same, and that is to deliver value to the customer as quickly as possible. The trouble is that there's no value delivered until the ship - and by that time, software might have done four things in the time it took hardware to do one. 

If you're familiar with software development methodologies, you'll note that the way hardware teams operate looks a lot like Waterfall. That's because it is waterfall. You need to gather requirements up front. If you need a hardware component to achieve a feature, it needs to be built into the product. If you change your mind, and try to adjust what you ship on an agile basis, you run the risk of breaking all sorts of other stuff by accident:

* Compliance
* Emissions
* Unintended consequences to other product functionality

I'd imagine that this leads to software orgs looking at hardware the same way a turkey would look at a velociraptor. About the same size, about the same shape, but one is a savage prehistoric relic. 