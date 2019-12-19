---
bg: "atmega_schematic.png"
layout: post
title: "Reviewing Bad Schematics as Interview Tactic"
summary: "help your EE candidates strut their stuff"
tags: ['electronics']
date: 2019-12-19
---

Here’s an interview tactic for electrical engineers that was recently used on me. I liked it enough to swipe it for myself, and have used it several times since to great effect. 

You set up your candidate with the following prompt: 

>“I’ve just given you a new circuit board prototype that’s back from assembly. Your job is to bring it up and test that it’s working correctly. You make all of the necessary connections, apply power, and almost immediately, you hear a ‘pop’, and smell smoke.” 

Then, you give them a fake schematic of the circuit board, and ask them to troubleshoot the problem. 

Here’s the test: the fake board schematic should be absolutely riddled with errors. All sorts of errors - errors that you’ve seen before, errors that you’ve heard about before, errors that have thrown your product development efforts for a loop. These errors should run the gamut of severity - you want a range of problems for the candidate to spot:

* glaring errors that will cause components to die
* medium errors that will work most of the time but cause problems in some units, and 
* subtle errors - things you could just as easily ascribe to differences in design philosophies between engineers

The idea is that you want to give your candidate a chance to look at a schematic and add some value on it. It's a way to see how much they see in your design, and, in the process, to get an idea of what kinds of issues *they've* seen before. 

# The Upsides

I think this test is super important, for a few reasons:

##### It’s a small, impactful work sample. 
It’s a chance for your candidate to do something that actually looks like the job they’ll be doing at your company. Not only that, it’s one of the most important parts of the job they’ll be helping you with. Good schematic reviews catch errors both dumb and difficult. The resulting output is functioning electronics, and, as a result, time saved for your organization on debug and testing. Isn’t that what you’ll be paying them for? 

##### It’s an easy way to demonstrate experience. 
It shows you, very quickly, who among your hiring pool has actually designed hardware before. Many engineers will come into an interview without direct experience in the area you’re hiring for, or coming from an area that isn’t hands-on in schematics. (I’ve learned now that some companies don’t even allow test engineers schematic access of any kind. WTF?!) 

##### It’s scalable for the seniority and nature of the role. 
You can tailor your bad schematic to be appropriate for the role. Looking for a digital engineer? Set up a question with a microcontroller, and ask questions about timing and terminations. Interviewing a power electronics specialist? Make a schematic for a lousy offline AC/DC converter, and ask them how it could blow up. 

##### It allows you to gather metrics on multiple candidates. 
I keep a text list of all the issues that a given schematic has, and I use this to keep score on the candidate's performance. You can keep a spreadsheet for multiple candidates' performance, and examine at a glance who got what questions right, and which questions (if any) were stumpers. 

# The Downsides

Even with all those benefits, it’s not a perfect test. Here are a few caveats for you to keep in mind:

##### Your schematic symbols should be as informative as possible. 
Your schematic symbols should include as much information as you can give the candidate. If your prompt includes a component breaking, you should have a component symbol on the schematic that will obviously break. A backwards diode that will short a rail to ground is an acceptable test. A current sneak path through a regulator displayed as a square block is sneaky, power tripping bullshit. 

##### Stress is a huge factor in interviews. Compensate accordingly. 
If they don’t find the obvious flaws, ask leading questions like “Can you see any other problems in this design?” Guide them to see the problems they are able to understand if there are some errors that are obviously out of their depth or experience. 

##### Don’t make a schematic more than one page. 
Seriously. Futzing with inter-sheet errors and multiple pieces of paper is just a waste of the candidate's time. This is more about electronics intuition and design experience than a candidate’s capacity to run a Design Rules Check. Your test should not fill more than one 11x17 inch piece of paper. Give them pens. Encourage them to doodle if that helps them track their thought process. 

##### It’s biased in favor of experienced candidates. 
This testing method is a lot harder for junior EEs to perform well on. Typically, you’ll want to include some errors that are subtle shibboleths for your specialty. This is a way to suss out candidates who are really experienced in the area of expertise you’re hiring for. Unfortunately, many junior candidates just don’t have the experience to spot errors that light up for a senior candidate like a neon sign. (My example of this: I have yet to meet a junior EE candidate who understands the need for decoupling capacitors in digital circuits.) And that’s fine! You’ll either want to scale the test schematic, or your expectations for their performance, accordingly. 

# Parting Ideas
Need some ideas for a crappy schematic? Here are some easy ones I can think up off the cuff:

* Current limiting resistors missing (BJTs, diodes, etc.)
* Shorting a potentiometer node to a rail
* Voltage regulators missing capacitors at input/output
* Missing pull-up/pull-down resistors on digital buses
* Missing decoupling capacitors
* Powering ADC reference voltages from a switching supply
* Transistors placed in wrong/backwards configuration
* No signal conditioning/buffer for analog signals into ADC
* AC coupling high-pass filter sets corner frequency too high into ADC
* Reset sequence of digital component is wrong 
* Antenna is missing a matching network/filter
* Clock/data lines swapped backwards in digital bus
* No pull-up/pull-down resistors on IC configuration pins
* No frequency compensation on unstable opamp circuits
* Improper gain settings on opamp circuits 


Got any tricks up *your* sleeve for this kind of interview? I'd love to hear them! 
