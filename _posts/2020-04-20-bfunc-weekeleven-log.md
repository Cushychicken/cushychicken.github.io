---
bg: "bfunc_proto.jpg"
layout: post
title: "bFunc - Project Journal - Week Eleven"
summary: "time for a short rest"
tags: ['electronics']
date: 2020-04-20

---

Here's the build log for week elevn of development for [the open source function generator I'm trying to prototype and build.](http://cushychicken.github.io/insane-oshwa-goals/) 

Do you want to try one out? Head over to [this page to get the download.](http://cushychicken.github.io/bfunc-call-for-users/) 

Each day's entry represents an hour's work per day - the hour before I leave for my job every weekday morning. (This is more accurately described now as "the hour before I start working on my _real_ job every day" given the coronavirus lockdown situation.)

I'll be taking this week (ending April 24, 2020) off of bFunc development as a little vacation. I'm gonna take a week to sleep in, write a retrospective, and work on a little consulting side project I picked up. bFunc development starts again in earnest on April 27, 2020!

This is my log for the week ending April 17, 2020. Here's links to the prior weeks' logs:

* [Week One](http://cushychicken.github.io/bfunc-weekone-log/)
* [Week Two](http://cushychicken.github.io/bfunc-weektwo-log/)
* [Week Three](http://cushychicken.github.io/bfunc-weekthree-log/)
* [Week Four](http://cushychicken.github.io/bfunc-weekfour-log/)
* [Week Five](http://cushychicken.github.io/bfunc-weekfive-log/)
* [Week Six](http://cushychicken.github.io/bfunc-weeksix-log/)
* [Week Seven](http://cushychicken.github.io/bfunc-weekseven-log/)
* [Week Eight](http://cushychicken.github.io/bfunc-weekeight-log/)
* [Week Nine](http://cushychicken.github.io/bfunc-weeknine-log/)
* [Week Ten](http://cushychicken.github.io/bfunc-weekten-log/)

[The design doc for this project](http://cushychicken.github.io/bfunc-design-doc/) is available if you're into design documents/philosophy. 

All of my hardware and software source files are on GitHub. [Check 'em out!](https://github.com/Cushychicken/bfunc)

# April 17, 2020

- Starting writing the retrospective for the first three months of bFunc work. 

# April 16, 2020

- This day’s productivity was lost to insomnia. 

# April 15, 2020

- Turns out [that post from Fahrzin Hemmati’s blog](http://blog.fahhem.com/2017/12/jump-to-emb-bootloader/) was just the ticket. The jump to the bootloader is working! As is the DFU reprogramming! 
  - Fahrzin’s secret sauce: define a section of memory in the linker script specifically to hold the bootloader magic number. Giving that memory its own section prevents it from getting zeroed out on reboot. Thanks Fahrzin!
- A few notes on this:
  - There doesn’t seem to be a way to software-reset the board back into its normal operating state. It seems to require a power cycle - getting unplugged from USB, and then plugged back in - to restart with the new firmware. 
  - It would be _really_ slick to make this an entirely software process, where the chip just reboots in its normal operating mode. This is a to-do for the backlog, at this point. I need a break from bootloader stuff for a while. 🙂 
- This is a really sensible point to get rigorous about code tagging and version tracking. 
  - The Embedded Artistry post I pointed to a while back was a great resource for how to version embedded code, but I’ll be honest - it was a little further into the `git` weeds than I’ve currently ventured. 
  - Specifically - it relies on `git tag`, which I’m very unfamiliar with. 
  - [This post](https://medium.com/@emmabostian/using-git-tags-to-version-coding-tutorials-cf9ff28fad4f) was a great intro to `git tag` - it’s my reference from here on out. 
- A logical next step for this is to add in a little GUI functionality to handle a few other components of upgrading the firmware:
  - Grab the latest firmware image from the internet
  - Put the board into upgrade mode
  - Find the proper DFU device in the DFU registry
  - Reprogram the flash using DFU 
  - (Ideally) resetting the board from DFU 

# April 14, 2020

- I was stumped by my progress with the bootloader, so I asked a question on Reddit. 
- /u/farptr pointed out that I was trying to use an `__initialize_hardware_early()` macro that’s exclusive to µOS++, which I obviously don’t have as I’m not using µOS++. 
- /u/farptr made a great suggestion - just move that check for the magic value into `SystemInit()` instead. This works, and gets the part un-stuck, but never seems to branch to the bootloader successfully.
- I think what’s happening is that the variable that’s storing the magic number, `dfu_reset_to_bootloader_magic`, is getting stomped on and reset to zero by some earlier function in the startup script. 
- [This blog post](http://blog.fahhem.com/2017/12/jump-to-emb-bootloader/) shows a way to do that using a bootloader flag defined in the linker script. That seems like a reasonable fix. 

# April 13, 2020

- Diving into getting the USB boot loader working, and jumped-to through software
- Added an `upgrade` command to the parser that resets the MCU in boot loader mode
  - Does this device show up when we run `dfu-util -l` to list all DFU devices? 
  - It does when we re-strap the BOOT0 header. 
  - When we run the `upgrade` command, however - nothing!
  - After doing some research - I think I’m getting the stack pointer location wrong
  - The default stack pointer location is at the first address of the system memory block - 0x1fffc800
  - We can check what’s there using `gdb` - yields a value of `0x200014a8` 
  - A little more help from `gdb` reveals that we’re never getting to `__initialize_hardware_early()` - the system never calls the function! 
  - Makes me wonder - where is `__initialize_hardware_early()` defined, anyway? 
  - Running `grep -rnw "__initialize_hardware_early" -r ./` in my project directory doesn’t show any references except for the ones in my source code and linker files

# April 12, 2020

- Implement [this code](https://stackoverflow.com/a/36793779) to reset into the STM32 ROM bootloader