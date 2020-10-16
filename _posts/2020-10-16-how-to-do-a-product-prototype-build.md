---
bg: "electronics-manufacturing-line.png"
layout: post
title: "How to do a Prototype Build for a Hardware Product"
summary: "professional prototypes, promptly processed"
tags: ['working on hardware']
date: 2020-10-16

---

Building your first set of product prototypes is a really exciting milestone for a hardware company. It’s the first chance to see the hard work of a lot of crossfunctional contributors bear fruit, in the form of a real, tangible, functioning item. It’s also a great opportunity to learn about the “meta-product” - the process of building your product at scale. 

This post captures a lot of what I know about how to run a good cross-functional hardware prototype build, from spending the better part of a decade in the consumer electronics industry. 

It's based around the assumption that you are:

* part of a cross-functional team,
* designing a hardware product in subcomponents, by specialists, 
* who are looking to ramp up production of that product at a factory once the development process is complete. 

Most of this generalizes pretty well for small-ish teams (5 to 25 people) building small-ish runs of product prototypes (50 or less). It's deeply informed by my experience in consumer technology, but I think it's a reasonably good guide for any hardware company whose goal is to get a product built through a contract manufacturer at any reasonable scale. I don't know that it generalizes well to a massive hardware manufacturer like Apple or Amazon or Samsung. Then again, they make hardware prototypes too, and it's typically not a good ROI to make thousands of units straight out of the gate. 

**Note:** I started this post before COVID was a thing, and it became inadvisable to get a large group of people in a room together. It’s one of my dearest hopes that COVID is gotten under control, largely so that this kind of work can happen again. In the meantime, I’d love to hear about people’s experiences building hardware prototypes in spite of the coronavirus pandemic. 

# Start with a process - even an incomplete one. 

A prototype build itself is a test - but not necessarily an engineering one. It's an operational test. It's an opportunity to learn about your process, and a great way to answer questions like: 

- How easy is your system to integrate? 
- How easily do all the pieces come together? 
- Is it easy to test all the subcomponents? 

To maximize the odds of success, it's good to have some idea of a process going in. Even an incomplete plan is OK.The point is that *something* is better than nothing, because you can at least see where improvements get made that way. Typically, each functional discipline comes up with some kind of plan for their subsystem beforehand. This plan can be really, really basic. Your final production assembly and build process is, without a doubt, going to look very different from the first prototype build. Set expectations accordingly. A line item of "screw the heatsink to the circuit board" is an acceptable, back-of-napkin process for a first prototype build. Producing a full fledged Standard Operating Procedure, fit for handoff to a contract manufacturer, is not a good use of time at this point. 

Lest there be confusion about the goal of your first proto build: *the prototypes are only half of what you seek to gain.* The experience of building the product is just as valuable as the hardware prototypes themselves. 

Hardware engineers are architects, not construction workers. It’s our job to produce *clear plans* for making *robust products.* At the end of the day, hardware engineering orgs aren’t responsible for building objects. We don’t make *electronics*, or *plastic parts*, or *antennas*. We make *instructions* for making those things. Our one true output is *documentation*. (And thank whatever gods you pray to that this isn't paper any more!)

# Track all of your subcomponents.

Even a relatively simple consumer product is going to have ~20 component pieces. Suppose you’re building a prototype run of 20 products. That’s 400 pieces of input goods going in.

Those subcomponents include:

- Assembled PCBs
- Firmware images
- Mechanical parts (both designed plastic parts, and COTS stuff like screws)
- Cable assemblies
- Antennas
- Test fixtures
- Test equipment
- Assembly equipment (Do you have enough hand tools? Do you have the *right* hand tools? No one wants to hold up the build because you don’t have the right Torx screwdriver size.) 

Know where all of these are, and what state they are in, before and throughout the build. It's especially important to track things like: 

- Location (Did the parts get to our facility? Where is it now?)
- Firmware versions (Did we get the latest code? Do we need separate images for test and production?)
- Test status (Did we run all of these components thru incoming quality checks?)
- Changes made (Did we have to modify any components after initial inspection? E.g. electrical reworks, mechanical modifications)

Knowing these things helps immensely in tracking down subtle bugs. This data can help identify patterns you can use to suss out hardware and software issues. At some point, you'll be asking questions like:

* Was that issue seen at your final assembly step a bad electrical connection? Or was it a consequence of a software bug? 
* Did _every_ unit show a particular streak of bad behavior, or just the units with the outdated firmware built into it? 
* Did we screen for that known hardware issue we found in an evaluation kit?
* Do units that fail vibration testing have Mechanical Modification Step C applied?  

This tracking system doesn't have to be complex. I've had great success with tracking systems as simple as a piece of paper that travels with a circuit board. I've also worked with systems as complex as multi-page Excel sheets, and Jira Epics. Mileage varies based on size of company and number of contributors.

# Have a bringup procedure.

The squares in the Quality department call this “incoming quality control”. Whatever. A rose by any other name. To paraphrase Andy Grove: you want to be catching defects at the lowest-value-add step. That, ideally, means finding issues in subcomponents rather than in finished goods. A little work on the front end goes a long way towards successful, functioning prototypes. It's also *wildly* less expensive, in terms of engineering time. (Think of how much time it took to fix those too-short wires in the Airbus A380. You know - [the ones they found out were too short, once they started putting pieces of fuselage together](https://www.nytimes.com/2006/12/11/business/worldbusiness/11iht-airbus.3860198.html).)

I like to do some real basic checks on my circuit boards before they go into a prototype - things like:

- Power
- Reset sequence
- Clocks
- I/O - correct voltage, no shorts to GND
- Peripheral interfaces (UART, USB, I2C, etc)
- Board-to-board connections 

Ideally, I do all of that *before* it gets buttoned up into a prototype. That way, if I borked something, we can rework it before it gets built into an enclosure. This saves vast amounts of time otherwise spent pulling the enclosure apart, retrieving the circuit board, and fixing it. 

A quick IQC procedure can be as simple as connecting some boards together and running some basic diagnostics. This stitch in time saves far more than nine in net build productivity. 

# Have a kickoff meeting.

It’s a great idea to get everyone in a room before the build starts, and have a brief kickoff describing the build process as you understand it. Spend an hour or two getting everyone on the same page, and building some collective agreement on the process. You'll want to assign some responsibilities to teammates on the “production line” here. 

Philosophies on these assignments vary. Sometimes, you want each discipline managing their own task. Sometimes, though, it’s fun (and instructive) to have a little shakeup, and have people work on something that isn’t their exact discipline specialty. You learn things this way that you’d never otherwise know. A great example is getting the mechanical engineers programming firmware. It highlights how a good programming process intertwines with easy access to debug interfaces, which is a skill that can't be taught - only won through experience. (Typically, through the misery of challenging debug adapter cable routing.) It also tests whether your process is robust enough to be managed by a non-specialist. 

This robustness is critically important in the consumer electronics world. Most consumer electronics products are manufactured in China. The typical Chinese CM floor assembly worker, in my experience, is:

* between 18 and 25 years old, 
* from Western China - typically a rural part of the country, and
* lacking in any technical or vocational training. (Most of this is acquired on the job.)

Thus, the bar for your process should be: so easy that a teenager who doesn't speak your language can do it.  

# Build it! 

This is the fun part. 

Get everyone in a room, and follow your process.

Put stuff together! See what breaks! See what goes right! 

See the finished goods, the fruits of your labor!

# Track bugs. 

You’re gonna find problems with your system by going through this build process.

Like I said before: that’s the point! You're learning about this process by doing this build. Failing to keep track of the problems is throwing away half of the value of the prototype build. 

Track these bugs! In a spreadsheet, if you have to! 

It's such an important step that I think it should be one person's job to record bugs. Anyone, at any step of the build, can go to the bug recorder, and describe an issue they've uncovered with the build process. Pile all of these issues, and documentation of them, into a Master Issues List. You'll need it later!  

# Have a closeout meeting. 

Just as you had a kickoff meeting at the front end of the build, you'll want to have a closeout meeting at the tail end. Get the whole team together, and review your work. What went well? What didn’t go so well?

Great prototype builds involve a sharing component. Make time for each contributor to share a “gone well” item with the group. It builds camaraderie, and trust. It’s also an opportunity for teammates to highlight things that they did well for the team to see. Otherwise, people might miss the hard work they’ve put in. 

Another component of this closeout should be a speedy run through of the Master Issues List. Spend a bit of time going through the list of issues found, and assign ownership for each issue. It’s typically obvious where problems exist, and ownership of resolution typically flows very plainly from that. As a result, this review should be quick-quick. Try not to spend more than 2 minutes on any given issue. Any issue where the ownership of the fix isn’t obvious, or sparks discussion on fix methodologies among the team, should get tabled for later discussion. If it's complex enough to merit brainstorming across the team, it's too complex for this speed-run review. Give it a temporary owner and discuss with the relevant parties after the build is complete. 

Be sure to save some time for open discussion at the end. Ask everyone: “Was there something we missed?"

Send everyone the Master Issues List (or assigned issues, if you’re tracking bugs in Jira or some other bug tracker) once the review is complete. That's the work the team needs to focus on now that the build is over. 

# Celebrate!

The wrapup of your prototype build is just as much a celebration as it is a meeting.

Have a happy hour or team dinner on the calendar afterwards. The team deserves a chance to celebrate everyone's hard work. 

# Keep Moving

The net result of your prototype build is twofold:

* fresh prototypes, hot off the lines, and 
* new knowledge about how to actually *make* your product. 

Start turning that knowledge into a first pass of manufacturing procedures. Draft some SOPs, plan for test fixturing, and think about investments you can make to eliminate assembly errors.  

Begin more aggressive testing of the product. Make sure your components are delivering on their specs. Continue filing bugs as issues arise. Start on verification testing. It ain't sexy, but it's necessary. 

Work to burn down the Master Issues List. Keep the feedback loop tight: associate schematic changes with bug numbers in your issue tracker! 

Stay focused on the end goal. It should be a lot easier now that you've got a tangible representation of it! 
