---
layout: post
title: "Figure Out Firmware Updates First"
summary: "the single best long term investment you can make in product team productivity"
tags: ["programming", "hardware"]
date: 2024-06-21
---

Most of the software I've written and released to the world is firmware. It's increasingly apparent to me, as I write more and more of it, that you should figure out how to ship a firmware update first, before you spend too much time on feature work. Or, really, anything else in your firmware. Why? Because it accretes so much value over time to be able to push updates quickly. It's agile, and not in the project management sense. It gives you a vehicle to make quick updates and iterate rapidly. Lest I be accused of regurgitating buzzwords here, I'll be real specific about my meaning:

* **Minimal friction:** I keep seeing a persistent antipattern in the firmware world of "waiting until it's right" to cut release versions. Part of this is the right and laudable instinct to not ship half-baked software. However, I'm convinced that more than a bit of this is attributable to the friction required to do a software update. Nobody wants to remember twenty lines of high risk `scp` commands and hex offsets to properly reflash your eMMC from userspace. If you get it right - great! But if you get it wrong, you have to deal with the _even worse_ hassle of going through a factory reset to restore your prototype from being a paperweight.
* **Fast bugfixes:** when you observe an issue, and identify the failing version, you're already half of the way towards fixing the problem. Find short hash, create bugfix branch, iterate, test, deploy. (Corollary: you need to be able to identify software versions quickly. This is its own essential guidepost that I'll eventually wite a separate post on.)
* **Aligning incentives towards continuous integration:** fast updates mean fast test cycles. Theoretically, this means that you should be able to get stuff into `dev` or `deploy` or whatever you call your main development branch a whole lot faster. This is all predicated, however, that your code review and test coverage is a sane process. 

This is one of those lessons that we in the embedded world can stand to learn from those fancy cloud folks. Webdevs and cloud app developers learned the lesson of frictionless deployment so well that they created a whole subset of programmer just to deal with it. (Hi, DevOps/CloudOps!) This isn't to suggest that it's simple - rarely does a whole industry crop up without a painful and challenging need to be addressed. It's just that, with firmware, you have the added bonus of _physically installing your software in a machine_ on top of the process off effectively getting code from `dev` to `prod`.

The best illustration of my meaning is, without a doubt, a short parable or two from my own career. Here's two anecdotes

# Story One: The Warning

I used to work at a little startup that made robots. This robot was mostly composed of pre-made systems that we integrated together, with a few bits that we designed some custom electronics for. (Nothing outrageously special - mostly some power management we couldn't get COTS, plus some real form factor constrained motor drivers.) There were about a half dozen separate software systems that had to interact with one another on this robot.  All of these systems had to have their software updated by hand - typically by either:

- connecting an Ethernet cable to a box, `ssh`'ing into it, and pulling new code onto the box, or 
- connecting a debugger to a circuit board, and downloading new firmware into the microcontroller

This doesn't seem like a big honking deal on face, but physical access to these systems was challenging. The hardest ones to get to required about an hour of assembly / disassembly to do code updates. The robot almost always broke if the software in two parts interacted incorrectly with one another, generally by either crashing bizarrely in the midst of operation, or by failing to start operating altogether due to some API mismatch between two software versions. This often resulted in a frantic log crawl to determine the source of failure, followed by a round of software updates to all the suspect components to ensure known and solid versioning of all of the software components. This inevitably meant a lot of robot disassembly to plug in Ethernet cables or J-Link debuggers into the right slots. 

To the credit of this particular org: there was reasonably good discipline with regards to weekly release integration tests. Once a week, we pushed new software to each part of the robot, and forced the thing to run its hyper specialized task in order to collect metrics. However, the rigamarole of firmware updates made our weekly release tests a very challenging endeavor. The release tests nominally happened every Tuesday morning at 10 AM. Or, at least, they theoretically did. I don't recall a single instance of one starting before 4 PM. Several of them didn't start until after 8 PM. They were always "all hands on deck" affairs. Everyone in the company dropped what they were doing to get the robot to work, all day, every Tuesday. (Well, everyone except for the CEO, who was always off doing "business development", or commandeering the office shower stall for private Zoom calls.) To phrase this in terms of opportunity cost: about half of the fifteen employees were _just working on updating the robot's software_ once a week. This was instead of doing their other assigned task: mechanical engineering, firmware development, computer vision pipeline development, etc. 

At a certain point, I realized that our system was architected in such a way that one component - the little industrial computer that was the brains of the robot's operation - had access to every single other component in the system. It could talk to everything else on-robot through via some hardware interface: either a CAN bus or an Ethernet connection. Microcontroller-wise, we were an entirely ST Micro shop, which meant every MCU in the system had an OTP bootloader baked into the chip. We had a route to push software updates to each chunk of the system, with a pretty minimal amount of software engineering. What an obvious area for optimization! Automating updates equals:

- one central source of truth for all software on the robot, with all sub-images getting baked into the industrial SBC image, 
- no more prototype rot from assembling and disassembling the thing, 
- huge time savings - no assembly or debug required! 

On top of this, I managed one embedded software engineer who was dying to get promoted to a Senior title. This was an ideal task for her to show she was batting at a higher level - spec out an update framework, get buy-in from management, then go implement updates. So, we got cranking on writing the spec, proving out the concept of the updates, and all of the other organizational rigamarole needed to make things like this happen. The shocking shutdown came when the CTO found out about effort, went ballistic, and shut down the effort. His rationale: "the EE team stopped working on features that make the robot better. This is unacceptable". I tried explaining that we were spending half of the company's time, one day a week, just to push machine updates rather than doing feature work. This didn't go anywhere. 

Three lessons I took away from this:
 * if something is obviously a good idea, it's generally a better idea to beg forgiveness than ask permission.
 * [Feature factories](https://cutle.fish/blog/12-signs-youre-working-in-a-feature-factory) are real, miserable, and not at all.
 * Some people simply do not understand the opportunity cost of peoples' time. 

# Story Two: The Example

I cut my teeth as an engineer and product developer working at a well known multinational consumer audio brand. One of the things they did incredibly well was firmware updates to hardware systems. From a Linux console on the device, you had one command, `upgrade -s https://www.upgrade-server.com/image-name.dmg`, and the device would automatically take care of a firmware update for you. 

In fairness: this was the product of a _ton_ of good work and tech accumulated and stabilized over time. Just a few of the examples I can think of:

* Sane branching, review, and merging procedures
* Good code review process
* CI/CD build processes with dedicated servers for compilation
* Dedicated, globally accessable firmware image servers for prod
* Image servers for dev builds accessible to _anyone_ on the company intranet 
* Signature and authentication of firmware images by servers
* Authorization and verification of firmware images 
* A procedure for making exceptional, signed builds for security-sensitive feature work
* A single application for firmware updates from the device, easily executed at the command line 
* A simple, centralized way of checking firmware version on the device, from a command line interface or a self hosted webpage on the device. 

I could go on and on, but all of these meant it was a breeze to upgrade software. It required so little mental energy as to be almost effortless. This is in stark contrast to a lot of the other places I've worked since. 

# Tools

I don't want to make firmware updates sound like something that is trivially easy - there is a ton of overhead to ensuring that your product is secure, and that you're achieving the right balance of accessibility vs. code security. However, I'd cautiously suggest that there is much of the firmware update process that can be considered a solved problem - especially if you're just looking for a faster way to get new images onto a target device for development purposes. Here are a few of the tools I've used, or have come recommended to me from engineers I trust:

* Like I mentioned earlier, STM32 microcontrollers all contain a bootloader flashed into OTP in a section of memory. This is true for all STM32 devices I've worked with, from tiny Cortex-M0+ devices to honking great Cortex-M7 beasts. All of them are capable of updating firmware over UART, SPI, or I2C. The more advanced chips with richer peripheral sets frequently support updates over CAN, Ethernet, or USB, as well. These bootloaders are pretty straightforward to access via either a boot mode or a jump to a specific address - check [ST Micro's AN2606](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://www.st.com/resource/en/application_note/an2606-stm32-microcontroller-system-memory-boot-mode-stmicroelectronics.pdf&ved=2ahUKEwiw2_aZuOqGAxXlv4kEHV4ZDX4QFnoECBgQAQ&usg=AOvVaw1jKOHIztGUGKeekkYWE3_F) for more specifics on accessing the bootloader, and the protocol guides for updates via [UART](https://www.st.com/resource/en/application_note/cd00264342-usart-protocol-used-in-the-stm32-bootloader-stmicroelectronics.pdf), [SPI](https://www.st.com/resource/en/application_note/dm00081379-spi-protocol-used-in-the-stm32-bootloader-stmicroelectronics.pdf), or [I2C](https://www.st.com/resource/en/application_note/dm00072315-i2c-protocol-used-in-the-stm32-bootloader-stmicroelectronics.pdf). 
* The folks at Memfault, true to form, remain ahead of the curve on embedded systems blogging, and published this great guide a few years back on how to implement richer firmware updates for when your system grows out of the stock bootloader offered by STM32 or some other vendor. [Highly recommended reading!](https://interrupt.memfault.com/blog/device-firmware-update-cookbook)
* `swupdate` is a fantastic open source package for managing firmware updates on embedded Linux systems. It's [currently developed by the Bootlin team](https://github.com/sbabic/swupdate) as open source software. Bootlin has [a great guide](https://bootlin.com/blog/tag/swupdate/) for implementing `swupdate` on an STM32MP1 embedded Linux SoC. They also work to maintain a `meta-swupdate` layer for [easily adding updates to a Yocto distribution](https://sbabic.github.io/swupdate/building-with-yocto.html). Note: at the time of this writing, my current project is using `swupdate` and `meta-swupdate`, with tremendous success. Firmware updates are easily pushed to a convenient, self-hosted webpage at a known device port. It is drag-and-drop simple!
* Mender.io is a comparable offering to `swupdate` that offers updates-as-a-service. While I've never personally used or implemented it, it comes highly recommended to me by at least two engineers whose opinions I trust!