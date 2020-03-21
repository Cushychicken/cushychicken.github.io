---
bg: "bfunc_proto.jpg"
layout: post
title: "bFunc - Project Journal - Week Seven"
summary: "want to file bugs against my crappy project?"
tags: ['electronics']
date: 2020-03-21

---

Here's the build log for week seven of development for [the open source function generator I'm trying to prototype and build.](http://cushychicken.github.io/insane-oshwa-goals/) 

**Note:** The prototypes are just about ready for human consumption!! Do you want to try one out? Head over to [this page to get the download.](http://cushychicken.github.io/bfunc-call-for-users/) 

Each day's entry represents an hour's work per day - the hour before I leave for my job every weekday morning.

This is my log for the week ending March 21, 2020. Here's links to the prior weeks' logs:

* [Week One](http://cushychicken.github.io/bfunc-weekone-log/)
* [Week Two](http://cushychicken.github.io/bfunc-weektwo-log/)
* [Week Three](http://cushychicken.github.io/bfunc-weekthree-log/)
* [Week Four](http://cushychicken.github.io/bfunc-weekfour-log/)
* [Week Five](http://cushychicken.github.io/bfunc-weekfive-log/)
* [Week Six](http://cushychicken.github.io/bfunc-weeksix-log/)

[The design doc for this project](http://cushychicken.github.io/bfunc-design-doc/) is available if you're into design documents/philosophy. 

All of my hardware and software source files are on GitHub. [Check 'em out!](https://github.com/Cushychicken/bfunc)

# March 20, 2020

- What’s the full set of commands I can pass this thing via serial port? 

  - `sine` - sets a sine wave as output
  - `square` - sets a square wave as output
  - `triangle` - sets a triangle wave as output
  - `idle` - idles the output
  - `freq` - sets the frequency of the current waveform 
  - `phase` - sets the phase of the current waveform

- Graynomad’s post on AVRFreaks is instructive on how to stack this up into command table 

  - Make a structure for each command:

  

  ```c
  typedef struct command {
    char cmd[10];
    void (*func) (void); 
  }
  ```

  

  - Make an array of command `struct` objects to compare against 
  - Loop through that array when the `return` key is pressed
  - `strcmp` through the `.cmd` member of each object in the command table 
  - Call the associated function pointer when `.cmd` and the input string match

- How does the behavior change for the different commands called? 

  - It’s not quite as straightforward as the AVRFreaks example
  - The behavior of the function changes based on the contents of the first parameter
    - `sine` / `square` / `triangle` each expect a freq and phase arg
    - `idle` expects no args
    - `freq` / `phase` expect one numeric arg

- This makes me think that the better way to implement Graynomand’s underlying command struct is something like this:

  

  ```c
  struct command {
    char cmd[10];
    enum states state;
  }
  ```

  - That way your command parsing table is just populating an instance of `struct ad9837_ctrl_reg`
  - The command completes by transmitting those bytes via `HAL_SPI_Transmit()`

# March 19 2020

- Lost my working time this morning due to some insomnia, and sleeping a little late to compensate 

- I did manage to find some time to plug the board into a Windows laptop, and everything worked fine! 

  - Backspacing, incredibly, works as expected! 

  - On my Mac, it does not set the cursor back. Need to figure out why. Guessing it’s from the serial consoles sending different characters for the “backspace” key

    - …but my Mac doesn’t HAVE a backspace key - it’s a “DELETE” key!

    - Here’s [a useful page that explains more of this](http://www.macfreek.nl/memory/Backspace_and_Delete_key_reversed#Screen)

    - [This GitHub Issue](https://github.com/sorin-ionescu/prezto/issues/61) also has a useful explanation 

      

# March 18 2020

- First step - determine waveform setting/action based upon first tokenized subfield 
  - Positional parsing/setting 
  - Ex: `sine 10000 45` produces a 10kHz sine wave with a 45 degree offset  
  - Suppose we split that into three space-delimited substrings 
  - Positional parsing means that the command line knows:
    - field[0] == WAVEFORM
    - field[1] == FREQUENCY
    - field[2] == PHASE
- One way of doing this is just keeping an integer variable that keeps track of the positional array via pointers 
  - This works, but is starting to overload `ProcessCommand()` a bit. 
  - Should probably break out into subfunctions: 
    - `ParseWaveform()` - takes first subfield as argument, returns `enum states` for new state (or NULL/-1 for no change/input error) 
    - `ParseFrequency()` - takes second subfield as argument, returns integer frequency (or NULL/-1 for no change/input error)
    - `ParsePhase()` - takes third subfield as argument, returns integer phase (or NULL/-1 for no change/input error)
      - Should likely expand to phase as a `float` but an integer is good enough for now
- Checking the size of my binaries - Using all these `string.h` function calls has me idly concerned that I’ll run out of flash.  
  - Some great explanation on interpreting `arm-none-eabi-size` results [from the folks over at Memfault](https://interrupt.memfault.com/blog/best-firmware-size-tools)
  - Consider swiping Francois’s size shell script - that’s a clever trick! 

  - The Memfault post also included a link to [a Google tool for easy binary size analysis too: Bloaty McBloatFace](https://github.com/google/bloaty) 

# March 17 2020

- Working to include `<string.h>` and a parser based on `strtok()`
  - Easier than I thought it’d be - `strtok()` replaces the delimiter character with a null character, so you can pass the returned pointer to a `printf()` function. 
  - In my case, I can save a pointer for the start of a delimited string, and use the pointer returned by `strtok()` to figure out the length.
  - `length = token_pointer - start_pointer;`
- Parsing in `ProcessCommand()` is working now! 
  - Can delimit the inputs of `UserRxBufferFS` by space separators
  - Next step is to compare the subfields of `ProcessCommand()` to a command table 
  - Next step after _that_ is to execute function calls based on the delimited strings
- Cool! Particularly productive hour of work. :D

# Mar 16 2020

- Starting again on command parsing 
- We can load up a buffer in memory using the USB CDC interface - it’s time to split it into substrings and make decisions based on those substrings 
  - `strtok()` is a useful function for this, but requires the `<string.h>` library, and I’m not 100% sure we can use that here  
  - Wrote a simple parser function that prints space-delimited substrings 
  - Note: when you hit “enter” on a screen session to process command, it’s `case ‘\r’` that the switch statement breaks on 
- String manipulation and parsing is proving to be the most challenging part of this project thus far XD 
  - I’m not bad at hacking through register settings and peripheral configurations, but old school string parsing is a subset of computer science curriculum I think I may have skipped over entirely
- Man, fuck it - do I wanna write a whole parser? Not really! 
  - Let’s figure out how to add `<string.h>` to this beez. 
  - Well - it doesn’t choke when we add the `<string.h>` include statement! That’s a start!