---
bg: "bfunc_proto.jpg"
layout: post
title: "bFunc - Call for Users"
summary: "want to file bugs against my crappy project?"
tags: ['electronics']
date: 2020-03-17

---

Hiya folks. bFunc is getting to an exciting stage. I'm just about ready to let *actual other human beings use it.* So, I'm going to one of the best places I know of to find actual human beings: the internet! 

***This is a call for users. If you want to test and use a hastily-thrown-together function generator made by a guy on the internet - now's your chance!***

If you're interested, please shoot me an email at my blog address saying so. The contact link is up there at the top of the page! [I'll even link right here, for ultimate convenience!](mailto:cushychicken@gmail.com) 

I've sunk about $2,000 into this project so far, so if you'd like a board, I'd like a little help from you offsetting that cost. Here's what I have in mind:

* **Edit (5/13/2020):** Turns out - flat rate shipping in the US is moderately expensive! And in COVID-19 times, a trip to the Post Office is risky. I prepaid for postage for the last remaining chunk of boards to avoid further trips to the post office. To help cover that, I'm asking for $10 for shipping in addition to the $40 per board. Happily, multiple boards will fit into one envelope, so feel free to stock up! ~~If you'd like a board, I'd ask that you pay $40 USD to help me offset the cost of the eval hardware. I'll cover the cost of flat-rate shipping anywhere in the US as part of that $40.~~ If you're international, we can work that out on a case-by-case basis. 
* If you'd like to work on the software for bFunc, I'd be happy to refund your $50 USD if you can make three pull requests against some features I've been planning. (I'll be sure to deploy the feature list in the next few days here.)

Lest this dissuade you: ***I'm more interested in people using bFunc at this point than I am in making money off of it.*** If you think you can convince me to give you one for free, or a reduced price, you probably can. I won't discourage you from trying! 

# What You'll Need

## Essentials

These are the things you'll need to get any use out of bFunc:

* ***A USB-A to USB-microB cable.*** This is how you communicate to the board, and also how it receives power. If you own any modern, battery-powered consumer electronics device, you've probably got this already. 

* ***A computer with a USB port.*** You'll need a computer that's capable of connecting to a USB CDC device. I'm not 100% sure how to spec out your computer's capability, but I feel reasonably comfortable saying that, if your computer can connect to an FTDI or similar virtual com port emulator chip, you should be in good shape. 

  Hardware wise, your computer should at least have a USB 2.0 port, and support USB Full Speed. I doubt this will be a problem for most folks - this is USB hardware that any given Raspberry Pi can support. 

* ***Hardware to connect bFunc to your target design.*** You'll need some way to jumper the output signal from bFunc to your target design. We've handily included two connector options:

  * ***A BNC cable.*** BNC is the de-facto standard for electrical lab equipment connections. This is what you'd expect from an industry standard function generator. Besides a cable, you'll need a little more hardware to hook into your design.
  * ***Some 0.1" female-to-male headers.*** This is likely the simpler option if you're not planning to hook bFunc to an oscilloscope or other piece of test equipment. Standard 0.1" headers/jumper wire will do nicely.

## Nice-to-Haves

These are things that you don't *have* to have, but they *will* make your life easier:

* ***An ST-Link debugger, and a debugger cable.*** Knockoff ST-Links are cheap and readily available on Amazon. (Even cheaper if you get them from Alibaba!) A debugger cable makes the debugger-to-board connection even easier to use. 

  If you want to contribute to the codebase, an ST-Link is an Essential item, not a Nice-to-Have. I don't have any other means of updating the firmware on the board at this point. (Lest you misinterpret this - I'd *love* your help contributing to the codebase.)

* ***Some basic rework capability.*** It will help you out immensely, should you come across a problem, to be able to solder the odd wire onto the board. 

* ***An oscilloscope.*** Really helpful in confirming that the damn thing is actually working as expected. I have a Rigol DS1054Z that I'm very happy with. 