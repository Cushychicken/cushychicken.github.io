---
layout: post
title: "NAND Flash: Device Architecture, Pt. 2"
date: 2015-07-30
---

## Introduction
Our last article dealt with stringing together floating gates into bytes, and into arrays. We're going to take that one step further in today's post, where we'll see how we can turn this whole system into a working memory chip! 

(If you need a review, parts [one](http://cushychicken.github.io/nand-pt1-transistors/), [two](http://cushychicken.github.io/nand-pt2-floating/), and [three](http://cushychicken.github.io/nand-pt3-arrays/) are linked here for your convenience.)

## Turn the Page
Our last article had a really brief mention of a concept we'll look at in closer detail here: the NAND _page_. 

<div align="center">
<img src="http://www.intechopen.com/source/html/19378/media/image2.jpeg"/>
<p align="center"><em>Schematic View of NAND Array (Courtesy of InTech)</em></p>
</div>

A page is the smallest quantity of data that you can read or write to at a time in a NAND Flash array - generally, 512 or 2048 bytes. This is a direct consequence of the method by which data is stored in NAND Flash. Recall that NAND Flash data storage directly depends on electrons trapped in a floating gate. To force electrons into that floating gate via tunnelling, there needs to be a large voltage applied to the wordline. The easiest way to manage this is a paged architecture.

To execute a write in a page cell, the voltage of the word line must be elevated well above the transistor's threshold voltage, while simultaneously pulling the bit line and source line of the desired bits to ground and activating the select gates. This exacerbates the potential difference between the word line and the floating gate, causing electrons to tunnel into it. This lingering charge will cause a channel to form in that cell during the next page read, meaning that this bit is now valued at a binary zero. Writing a binary one to a cell is a little simpler. Nothing needs to be done to the floating gate of the cell - all the circuit needs to do is inhibit tunnelling to the cell. This is as simple as not activating the select lines, which makes the potential difference between the word lines and the "one" valued cells much lower, thus inhibiting tunnelling. 

(**_EDIT - Tuesday, August 4, 2015:_** [Reddit user /u/neerok correctly pointed out](https://www.reddit.com/r/ECE/comments/3fctte/more_on_nand_flash_architecture_pt_4_in_a_series/ctonn3t) that the bit lines play a much more active role in programming than I previously mentioned - when a bit is programmed, the bitlines are pulled to ground to help exacerbate the potential difference needed for electrons to tunnel. When a bit isn't programmed, the bit line of that transistor is pulled high to minimize the potential difference - and resulting electric field - across the cell activated by the word line. Thanks for the clarification!)

## Blocked Out
A typical NAND chip is made up of thousands and thousands of pages, which are arranged by another architectural sublevel called _blocks_. Blocks are generally made up of 32 to 64 NAND pages, and unlike other memories (like DRAM or NOR), are the smallest number of bytes that can be erased at any time in NAND Flash. Why is that? 

Let's recall the floating gate transistor, and how we move charge onto the floating gate - by forcing electrons to "hop" into the floating gate from the word line through tunnelling. To erase a NAND cell, the reverse must happen - the floating gate must be purged of electrons through tunnelling, but _in the opposite direction_. Now, instead of moving from the control gate to the floating gate, the electrons make the reverse jump. By simple inference, you can probably infer what makes this possible: a high voltage. Like everything in this process, this voltage is the reverse of what writes the cell; therefore, the potential difference must be from the transistor's _substrate_ in relation to the transistor's control gate. 

Think back a moment to the layout of a NAND Flash page. How can this voltage elevation occur locally to a single cell in a NAND page? The answer is - you can't! By elevating the voltage in the substrate, you are forcing all charges in all floating gates in that section of the substrate out, and setting all of the bits back to a value of binary "one". This is the deciding factor of how blocks are arranged in NAND Flash - it's the smallest area of the silicon die that the voltage can be safely elevated locally for the purpose of erases. 

## System Architecture

Now that we know how NAND is organized on a low level - from floating gates, to pages, and subsequently blocks - we can take a step up the ladder, away from all of those pesky device physics, and take a look at the system as a whole. Here's a nice block diagram showing a system level perspective of a NAND Flash device:

<div align="center">
<img src="http://www.mouser.com/images/microsites/S34ML0x_blkdia.jpg"/>
<p align="center"><em>Block Diagram of NAND Flash Chip (Courtesy of Mouser)</em></p>
</div>

As you can see, outside of the array itself, the device isn't particularly complex! A few decoders, a few buffers, and some simple registers and logic to store addresses and execute chip functions. This block diagram is pretty nice in that it shows the program/erase controller that generates high voltages required for programming and erasing parts of the chip. This is the thing you, the engineer, are interacting with in your day to day life. It can come in many forms - as small as a chip on board an embedded system, or as big as a subcomponent of a solid state drive. Reads, programs, erases, and other functions are all piped asynchronously through the following signals:

* **I/O[0:7]**: The general purpose I/O bus used to interface with the chip. All data and commands written to the chip pass through this interface; all data read out of the chip comes out of it. 
* **Write Enable (WE#)**: NAND is asynchronous, and the write enable functions as the clock that latches a byte on the bus into the device. 
* **Read Enable (RE#)**: After a read is executed, the NAND's onboard I/O buffer will be full of data, which needs to be read out. The read enable is the latch that data from the I/O buffer onto the bus. 
* **Address Latch Enable (ALE)**: when high, signifies that the byte on the bus is part of an address in the NAND chip. 
* **Command Latch Enable (CLE)**: when high, signifies that the byte on the bus is a command byte to the NAND chip. 
* **Write Protect (WP#)**: prevents data from being written into the device when low. 
* **Chip Enable (CE#)**: as the name suggests, it allows the master device to talk to the chip. The NAND will ignore all bus traffic while this line is high. 

## Wrapping Up
It's been a long series of posts, but I have great news for you - we _finally_ have enough information to start getting to the interesting problems with NAND Flash. Now that we have an idea of what's going on top to bottom in the device, the next post will be all about how NAND Flash breaks - what goes wrong, and what that means for you, the end user. Stay tuned!

## Further Reading
The Open NAND Flash Interface (ONFI for short) is the standards consortium that governs NAND Flash interfaces. It was started by a number of semiconductor companies in the interest of providing a standardized NAND interface for end consumers. The net result of this is that pretty much all NAND producers follow this bus protocol. It's done a lot for elevating NAND to the same level as DRAM - more of a commodity product than a specialty design. If you've got any interest in the standards, or want a closer look at the electrical specs behind the precise protocol, you can check out their website [here](http://www.onfi.org/specifications).