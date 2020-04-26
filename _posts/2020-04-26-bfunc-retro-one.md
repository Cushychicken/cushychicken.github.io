---
bg: "bfunc_proto.jpg"
layout: post
title: "I Designed and Built My Own Function Generator"
summary: "do you want to get one to try?"
tags: ['electronics']
date: 2020-04-26

---

I’ve spent the last two to three months working on bFunc, an open source function generator board. I've had a longstanding itch to design some little piece of open source hardware, and I needed a function generator for my own bench. I also got a little obsessed with direct digital synthesis chips, and I needed an excuse to use one in a project. 

This is my writeup for my work so far, and a little preview of what’s in store for the next phase of the project.  

# What’s a function generator?

I've gotten this question a lot - mostly from my friends and family members who aren't electrical engineers. I take that as a sign that I'm leading a very balanced existence. It's just as important to have people in your life that _don't_ know what function generators are, as it is to have people who _do_ know what they are.

Function generators are really common pieces of electrical test equipment. At a very basic level, they are used to generate the most common types of electrical waveforms: sine, triangle, and square waves. Those three types of signals are the bread-and-butter of a _lot_ of electrical engineering concepts. Having a known source for those kinds of waveforms is extremely useful in a laboratory setting. If you'd like a demonstration, [this YouTube video from the University of Nottingham](https://www.youtube.com/watch?v=Zink6v6TXk4) is a great introduction into what function generators do. 

More expensive function generators can do fancier things: noise generation, arbitrary waveform generation, and even RF modulation schemes (OOK, ASK, PSK, FSK). These are great, but I'd say 99% of their use and usefulness comes from those original three waveform types. 

# Why build one? 

A multitude of reasons. 

## I didn’t have one, and I needed one. 

This, on its own, is a terrible reason to make your own test equipment. Unless, of course, you work in some really niche field of physics, where you have no choice but to make your own test equipment. 

I’d started learning about direct digital synthesis chips a few months ago, and I thought they were cool. I'd also been tooling around with some benchtop circuit designs, but without a function generator, I had a hard time comparing their performance to LTSpice. Eventually, I decided “This isn’t that complicated.” So I started building a function generator based on a DDS chip. Scratching my own itch seemed like a pretty surefire way to motivate myself to complete this. Fortunately for me, this project scratched several itches: the need for a function generator, the desire to use a DDS chip, the will to do more embedded programming, etc. (More on the embedded programming front in a bit.)

## I’m not the only hobbyist maker without a function generator. 

I’ve seen enough workbench shots on /r/electronics to know that there are _so many makers_ online with very little proper test equipment. Moreover - there are so many of them who don’t seem to understand why it’s necessary or useful to have test equipment. There’s a lot of people who make projects without bothering to share really basic operational characteristics of their work. Doesn’t anyone care about frequency responses of their circuits? What about phase response?! 

I would also posit that many people can’t really afford name brand test equipment. I sure don’t have $1500 to shell out on [a Keysight function generator](https://www.transcat.com/keysight-technologies-53210a-106-53210a-106?st-t=adword_google_*&utm_source=google&utm_medium=cpc&adpos=&scid=scplp53210A-106&sc_intid=53210A-106&gclid=Cj0KCQjwyur0BRDcARIsAEt86IAFE60WNObquC151r4oAvNTocMto1Rsrk1eFaQNXfolar1b2AredVoaAl2nEALw_wcB). I’d have to imagine the same is true for a good chunk of hobbyist electronics makers on the internet.  

My hypothesis for making bFunc is really twofold:

* Many makers lack access to decent, affordable test equipment, and
* Many makers lack the understanding to know why they need these tools.  

I view this project as a great opportunity to kill two birds with one stone - give the maker community some better tools, and give them a tool that they can learn something from.

## I wanted to program more. 

I haven’t gotten a lot of chances to program lately. I was missing embedded C. It was time to scratch that itch again.  

Coincidentally, I’ve been hanging out in a lot of corners of the internet that really, really like ST Micro’s STM32 series of microcontrollers. I figured that this was another opportunity to kill two birds with one stone. 

## I wanted to try starting a (very) small business.

My original goal for this project was to prototype and build it in time for OSHWA Summit 2020. I was hoping that I’d be able to go to the conference and sell a few of these boards to people who needed a cheap little function generator to stimulate circuits they build. Why? Because I’ve never sold a thing in my goddamn life, and because I’ve spent so much time on Hacker News that I’ve been brainwashed into believing that entrepreneurship is the answer to all of your problems. 

Seriously, though. I wanted to try my hand at selling a product. It’s something I’ve never done before. I wanted to test my ideas with a market, and see how the market responded. I’m happy to report that, so far, the market has responded positively! 

## I wanted to learn about USB. 

Universal Serial Bus is _everywhere_. It charges our phones. It logs our keystrokes. It transfers our data between computers - try as Dropbox and Google Drive might to supplant it.

I believe USB is fundamental technology to our modern digital society. Technology like that is technology worth learning about!  

## I wanted a distraction.

OSHWA Summit 2020 was my self imposed deadline for having this project ready. 

On the bright side - I met my deadline! 

On the not so bright side - OSHWA Summit ended up getting moved to an all-digital conference due to COVID-19. 

As a result, I did not travel to New York, I did not end up presenting my work, I did not meet any other hardware hackers, I did not pass Go, I did not collect $200. 

This was a short term setback, but a long term win. For one thing - I had a nice little project to work on through the COVID-19 lockdown. This has been _really, really valuable_. Whenever I’m bored, or anxious, I can disappear into the basement electronics lab for a few hours and bang out a feature, or run some tests, or write some feature briefs. It’s like a jigsaw puzzle, but with a lot more data sheets and compiler errors. 

All that extra time invested has resulted in a better product, and a cooler, more useful little gadget. I’ve already managed to add a lot of significant functionality to bFunc in the time since OSHWA, including:  

- Adding version numbers to the codebase, and build IDs to my firmware.
- Adding bootloader support, so that the firmware can be updated via USB.
- Adding sawtooth wave functionality.

I imagine this level of functionality is only going to get richer and more complete with a few more months’ work.  

## I hate programming most test equipment.  

I’ve only ever interacted with expensive test hardware with shitty scripting interfaces. I hate SCPI in particular with an absolute passion. It seems to be the de-facto communication standard for most electronic test equipment. I’m certain that it’s only used because it’s got so much legacy momentum. I find it hard to remember, needlessly nested, and difficult to query on the fly. 

I wanted a chance to be the change I wanted to see in the world on the programmable test equipment front. How, you ask? By _writing my own damn test equipment serial interface_. 

# What did you learn?

The best part of this project is, without a doubt, all the cool stuff I’ve learned. All of these lofty goals produced a hell of a lot of learning. 

## Serial interfaces are not trivial.

I just assumed going into this project that serial console terminals were just a standardized thing that everyone had figured out, and that a single “right way” had been agreed upon. 

How very, very wrong I was. 

Every single operating system and terminal emulation package seems to have its own special set of control characters and functionality. I don’t understand it. This seems like something that should have been standardized years ago - but we all know how that turns out:

![](https://imgs.xkcd.com/comics/standards.png)

For example: you know what’s a huge pain to get right across multiple OSes? Backspacing. Let me explain: 

- Macs send a DEL character (`0x7F`) when the backspace key is hit. This is because the keyboard key that is normally “Backspace” is “Delete” on Mac keyboards. 
- Windows computers send a backspace character (`\b`) like normal machines. I’ve read some forum posts online that suggests that this behavior varies across terminal emulation software. PuTTY, my terminal emulator of choice, performed like a champ, and backspaced and erased characters in the buffer flawlessly. I do wonder, however, when I’m going to get a bug filed for terminal incompatibility.  

I find it a little strange and surprising that Windows was much easier to get a serial terminal working as expected. Mac, I still haven’t figured out. (Is this something you know how to get right? I would absolutely love your help on this project if you do.)

This must be a harder problem than it seems - or perhaps maybe one I just don’t have enough historical context to solve completely by myself. 

## ST Micro is serious about developer experience.

I could write a whole post about just this. I used to think that Microchip was the real pack of nerds' nerds in the microcontroller world. I can tell, just from the documentation, that ST Micro gives them a run for their money. Where to start? There are _so many_ things that telegraph how much they care about good experiences for their end users. 

### Comprehensive, detailed documentation.

The fact that it’s 2020 and I have to call this out as a plaudit is a real shocker, but, hey, there it is.  

ST Micro _clearly_ gives a hoot about good documentation. Their datasheets are _excellent_. 

What's more: ST wrote a hardware abstraction layer for their MCUs, _and_ a programming guide for it. It's pretty common for vendors to supply a HAL. I can't say I've ever worked with one who provided a decent, complimentary documentation package along with it. Bravo!

### A batteries-included, onboard bootloader. 

This single feature of the STM32 series is such a breath of fresh air. I could write a whole fanboy post just about the embedded bootloader. I’ve worked with so many microcontroller vendors who recommend buying the next largest part whenever we ask about bootloader capability, because “you’ll need the additional flash space to store the bootloader". Then, once we’ve tentatively agreed to shell out for the larger part, they casually mention that their bootloader reference design only supports UART and I2C - and that, if you want USB support, you’ll need to write it yourself. It seems that somehow, the protocol we want bootloader support for is _always_ the one the vendor does not have implemented. 

I have _no fucking clue_ why this seems like good business logic to semiconductor companies. 

Think about it. USB peripheral devices are only useful in the context of the USB host they are connected to. This being 2020, and most USB hosts being internet-connected personal computers, it’s almost inconceivable that you would not be able to:

1. Download new software from the internet to your personal computer, and then:
2. Download said new software from your computer to your USB peripheral. 

You have, at minimum, a 12Mb/sec data link to the peripheral, and an internet connection on the host. Why _wouldn’t_ you want the capability to upgrade the peripheral?!

Additionally - why would you intentionally limit the software update avenues available to your customers? At that point, you're just placing artificial constraints on your downstream customers, and limiting their options. 

On top of all this - ST made another excellent decision with respect to their bootloader. They store it in ROM. _The only way you're getting that bootloader out of that chip is cutting it out of the die._ That's absolutely crucial. By hardwiring the bootloader into silicon, you're guaranteed to always have an on-chip recovery method. 

Just including this one well-designed bootloader feature telegraphs a _lot_ about ST Micro to me. Common sense, personal experience, and conversations with lots of other firmware engineers lead me to conclude that _lots of companies_ want the option to update their microcontroller firmware in the field. It also turns out that many of those same companies don’t have the time, expertise, or sheer give-a-fuck to write their own bootloader support. And why would they? Bootloaders are _hard_ to get right, and _rife_ with edge cases! 

ST Micro has clearly done their homework on their market segment. They noticed the problems that their clients were having, and they showed enough respect for their clients to solve their problems. That’s called a _competitive advantage_, friends. 

### A huge, rich app note ecosystem. 

ST will spell out for you how to achieve nearly any low level use case involving their chips, software, or peripherals. Wherever ST comes short - the hobbyist community online will pick up the slack. 

It’s been truly wonderful in terms of an enjoyable project. There’s _just enough_ info on Stack Overflow and ST’s forums to make hunting for it fun, but not so little that achieving my application level goals starts to seem like an impossible task. 

### Codegen tools worth a damn. 

STM32CubeMX is a fantastic codegen tool. Part of this, I think, stems from judicious use of commented code fences. It’s really clear where your custom code should go - just stay within the handy `/* USER CODE */` comment fences! If you follow this guideline, it's _really easy_ to call on the codegen tool again later in the project without fear of your code getting stomped on. 

It seems like cheating, sure, but frankly - I kind of like knowing that I can lean on a codegen tool. Initializing a timer peripheral is not, in and of itself, an extremely valuable thing. It’s what you’re going to use that timer for that really counts. Let the codegen tool remember which bits to set. I’ll focus on application level functionality. 🙂 

### Competitive pricing 

ST doesn’t screw around with pricing for their chips. Digikey lists pricing at just over $2.00 per piece at 5k quantities for the STM32F103 line of chips. That’s a _great_ deal for a 32 bit micro running at 120MHz with 64k of RAM, and 128k of Flash. 

As an aside - I’ll be classifying this side project a _huge success_ if I manage to sell enough boards to get 5k volume pricing. 

## Let the market test what people will buy. 

Early on in this project, I read Rob Fitzpatrick’s _The Mom Test_. If you’ve ever considered building a product, I highly recommend _The Mom Test_. It’s got a lot of great, actionable suggestions for doing user interviews on your product ideas. 

Anyway - one of the things Rob points out in the book is how you should be cagey about telling people what your idea is. This predisposes them to weigh in with their opinions and their feelings, rather than their experiences. 

This was a lesson I learned the hard way when I submitted my initial design document to Reddit for feedback. Boy, did I ever get some feedback. This comment in particular was _real_ hard to read:

> *Frankly, developing a 1MHz generator makes zero sense to me if you want it to be anything more than a personal learning project.*
>
> *Especially when there are well documented kits using the well known XR2206 clones on AliExpress doing just that for $2-3 delivered. You can't compete with that, even when these don't have USB (why do you even need USB on a basic function gen?) and aren't "breadboard-friendly" (which is likely a silly idea - why would you want a generator taking up space on your breadboard instead of using 2 wires to connect it?). That the gadget is open source won't sell it, that's not a selling point for devices in this price category.*
>
> *And if you wanted to step up and develop a 10-20MHz generator then you are going to start hitting against the competition like the FeelTech FY6900 - a full arbitrary signal generator, going up to 60MHz and costing about 100 bucks. It does have a ton of problems but you can't build your own cheaper than this.*
>
> *If your goal is to learn new things, by all means, go ahead - this could be a fun project. However, if your intention is to design something for sale, as you have indicated, then you are in for a lot of disappointment.*

This comment nearly convinced me to throw away all the work I'd done and quit the project. 

That would have been a real shame, because this comment turned out to be wrong, in just about every single assertion they made.

So, why do I bring up Rob Fitzpatrick’s book? 

One naysayer on the internet is not a market. Don’t take one person’s strongly worded opinion over the response of your target market. 

## The hard parts of function generation are not digital. 

The digital portion of this project was _surprisingly_ easy. SI of the crystal is decent. Timing tolerance of the crystal is decent. SI of the SPI interface is decent. Getting the SPI driver working wasn’t super challenging either. 

The _analog_ electronics are where everything begins to get tricky.

I’ve already had one bug filed against this project on GitHub. What’s the root cause? _Not including an antialiasing filter_. Classic, junior-year-of-electrical-engineering mistake. But hey! That’s just as much an opportunity for the next revision as it is a slightly shameful shortcoming of the current hardware. 

Since we’re on the topic of embarrassing bugs: DDS chips rely on current mode DACs. I misjudged what this meant going in. I expected this to be a push-pull current source - pushed 3mA out, sunk 3mA in. What I got instead was a modulated DC current source. Midscale current is 1.5mA, which pushes out a maximum of 3mA out and a minimum of 0mA. As a result - the output of the DDS chip has a nice constant 0.3[V] DC offset! This is _definitely_ something I want to get rid of in the next build. 

For a third thing - it's a challenging task to design an output stage that allows for adjusting the signal’s output parameters. The following questions have sunk a lot of my mental bandwidth in the last few weeks: 

- How do I adjust the DC offset? 
- How do I adjust the amplitude? 
- How do I AC couple the signal? 

All of these questions must be answered outside of the digital domain. And they are all hard questions to answer.  Adding to that complexity is the fact that I have to figure out a way to answer them, and also control them in the digital domain. Remember - this is a digitally controlled function generator! Everything in the system is controlled by the MCU. 

# How did you do on your goals?

I'm so glad you asked! 

I set myself five goals at the start of this project:

1. Learn more about USB. 
2. Learn more about STM32.
3. Build, program, and ship something myself. 
4. Flex my programming muscles. 
5. Try selling a tiny little product. 

I'm happy to report that I've met all five of these goals to my satisfaction. Even better - several of these still have *tons* of room for further learning. I've barely scratched the surface of the STM32's capabilities, and I've only just begun to explore a small subset of USB's capabilities. 

# Next Steps

bFunc Rev2 is already on the horizon. I see a lot of opportunities to improve the current design. 

## Fix some connector problems. 

I ham-fistedly tore off one of the USB Micro connectors on one of the boards. A closer inspection revealed that the connector didn't really have proper retention pins, so it didn't really make use of the retention features on the PCB. I expect this to be a driver of some returns and customer calls in the first version.  

Part of the reason I managed to tear this connector off was the fact that the board was connected to a BNC cable at the time. The rev1 boards are _really_ small, and can't stand up when a BNC cable is plugged in - they don't have enough mass or dimension to counterbalance gravity's pull on the BNC cable. This is inherently going to put a torsion force on the USB connector. To address this, I'd like to change the vertical BNC connector to an edge mounted, right-angle BNC connector that hangs off the edge of the board. I don't think it will completely eliminate the torsion on the USB connector, but it should improve the strain on it significantly. 

## Design a better output stage. 

I really want to add amplitude control, DC offset control, and AC coupling. I also want all of those functions to be completely programmable. There's also a reasonable amount of functionality that I should add to make this a "proper" function generator that I neglected/straight up forgot to add in the first revision:

* Adding an antialiasing filter to the DDS output. 
* Adding an output driver, 
* Terminating the output driver with a 50 Ω resistor,
* Default DC coupling, with no DC offset. 
* Adding some sort of overcurrent protection.
* Adding some sort of Zero Crossing Detect functionality. 

## Add modulation capabilities. 

I'd really love to enable some basic modulation capabilities to bFunc. I could do this now, but I think I'm going to wait for the next build version - mostly because I'm pretty strongly considering changing from the AD9837 to the AD9834 chipset for the next revision. The AD9834 adds GPIO control of the phase select and frequency select registers. I have a mental concept of how this will enable PSK and FSK schemes, and I'm pretty certain that these will both be easier to do with GPIO control. I also have a hunch that the zero crossing detect feature I mentioned earlier will improve this as well. 

## Add noise generation. 

The STM32F103's onboard DAC has the capability to generate pseudorandom noise. I'd love to add that capability to the next version. 

## Improve GUI functions. 

I'd love to make the Python GUI richer, more responsive, and more helpful in generating an intended output waveform. Some ideas for doing this include:

* Adding an image of the waveform the generator is going to output, with timebase and amplitude annotations, 
* Adding mouse-over tooltips to buttons and fields in the app, 
* Adding a menu bar to move some of the less-used functionality from the GUI window into the system tray.   

## Improve GUI adoption.

As far as I can tell, none of my users are taking advantage of the (admittedly, rather thin) GUI client I wrote as a wrapper for the command line interface. I think I could improve this dramatically by doing a few things:

* Advertising its existence.
* Improving its installation/setup experience. Right now, it's only available as a command line launched program. I'd like it to be a program that launches from a Windows Start menu or the OS X dock. 

## Create some automated test fixturing. 

Programming every board, and doing outgoing quality control on each, has convinced me that this is an area ripe for some automation. It's a repetitive task, and it's kind of boring. That's the sort of thing that lends itself to making mistakes. 

I would kill for a pogo-pinned fixture that did some combination of the following:

* Provides power to the bFunc board, 
* Programmed the initial firmware image (either via ST-Link or USB DFU bootloader),
* Establishes a USB CDC connection with the board, and confirms the proper build ID was programmed. 
* Set a test waveform on the output, sample the waveform, and do a little math to confirm correct operation. 

I think this would be relatively simple to do with a Raspberry Pi and a hardware test fixture. I'd have to add some testpoints onto the next version to complement this. 

## Do a better job of generating demand.

One of my goals is to make this into a modest side business. This is proving to be the trickiest part of the process. 

At this point, I've mostly been generating demand through posting my progress on Reddit. I'm a little concerned that I'm spamming this channel to death; I _really_ don't want to get banned from /r/electronics, which I love. At the same time, though, it's chock full of the people I think are my customers: hobbyist electronics makers. 

I think it would make a lot of sense do to a rudimentary landing page test, backed with a few hundred bucks of Google AdWords spend, just to see if there's another market segment that I'm missing. 

It's also high time I spend some effort getting some more customers on board. This is just basic documentation on how to get started using the boards I've made, contributing software to the project, and maybe a wiki or forum to help people get their questions answered. 

# Conclusion

It's been a hell of a lot more stimulating than a jigsaw puzzle while in lockdown, that's for sure. 

I'd love to hear from you if you want a circuit board, have some design feedback, or just want to shoot the breeze via email. 