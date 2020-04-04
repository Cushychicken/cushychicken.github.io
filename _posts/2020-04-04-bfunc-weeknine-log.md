---
bg: "bfunc_proto.jpg"
layout: post
title: "bFunc - Project Journal - Week Nine"
summary: "rev01 is feature complete! boards shipping!"
tags: ['electronics']
date: 2020-04-04

---

Here's the build log for week nine of development for [the open source function generator I'm trying to prototype and build.](http://cushychicken.github.io/insane-oshwa-goals/) 

This week represents an exciting milestone - ***the Rev01 boards are feature complete!!***

Do you want to try one out? Head over to [this page to get the download.](http://cushychicken.github.io/bfunc-call-for-users/) 

Each day's entry represents an hour's work per day - the hour before I leave for my job every weekday morning. (This is more accurately described now as "the hour before I start working on my _real_ job every day" given the coronavirus lockdown situation.)

This is my log for the week ending April 3, 2020. Here's links to the prior weeks' logs:

* [Week One](http://cushychicken.github.io/bfunc-weekone-log/)
* [Week Two](http://cushychicken.github.io/bfunc-weektwo-log/)
* [Week Three](http://cushychicken.github.io/bfunc-weekthree-log/)
* [Week Four](http://cushychicken.github.io/bfunc-weekfour-log/)
* [Week Five](http://cushychicken.github.io/bfunc-weekfive-log/)
* [Week Six](http://cushychicken.github.io/bfunc-weeksix-log/)
* [Week Seven](http://cushychicken.github.io/bfunc-weekseven-log/)
* [Week Eight](http://cushychicken.github.io/bfunc-weekeight-log/)

[The design doc for this project](http://cushychicken.github.io/bfunc-design-doc/) is available if you're into design documents/philosophy. 

All of my hardware and software source files are on GitHub. [Check 'em out!](https://github.com/Cushychicken/bfunc)

# April 3, 2020

- First question of the day: how to set starting phase of the DDS chip to the same value?

  - Some ADI forum threads show that resetting the chip sets the DAC output to midscale 
  - That’s cool - what does it do to the phase accumulator register? Unclear!

  - Removing the SLEEP12 bit from the DAC does the trick 

- Another note - we’ll actually have to _halve_ the frequency of the input triangle waves to generate a sawtooth wave 

  - One period of a sawtooth wave is starting from zero, then going to its max value
  - Next period begins with a transition from max value to min value 

- We also need to offset each triangle wave by 270 degrees

  - Phase 0 == (phase + 270) // Allows triangle wave to start at the beginning of its upward ramp
  - Phase 1 == (phase + 90) // +90 is another way of getting to 270 + 180

- OK! We’re functional to the point where we’ve got a triangle wave that looks like a sawtooth wave for half its period. 

  - Next step: add a timer counter that can swap between phase register outputs at 1/f intervals 
  - That allows to generate a nice continuous sawtooth ramp, then swap over to a phase offset that’s at the start of the _next_ ramp

# April 2, 2020

- More cleanup/getting code in between STCubeMX’s code generation brackets
- This is all in service of adding the TIM2 peripheral to the project, which serves a higher feature still: sawtooth waves!!
  - I’ve plumbed in the code to handle serial commands, and loading the frequency and phase registers, but it needs an interrupt component to toggle between phase registers 
  - TIM2 must fire an interrupt at every half-period to modify the phase offset register in use
  - This is where we start needing to keep track of waveform state on the device
    - Sawtooth waves need to switch between phase and frequency registers - all other waveforms do not
    - Need to enable/disable timer interrupt
    - Need to initialize phase accumulator to zero (through a reset) so that it’s at midscale for the start of a sawtooth wave
    - Need to reset frequency/phase output registers back to freq0/phase0 at the end of the sawtooth wave (those freq/phase registers are defaults for other waveform outputs)

# **April 1, 2020**

- April Fool’s Day has officially been canceled this year, so I figured that now’s the right time to announce that Keysight has acquired my fledgling OSHW effort in an all-cash deal.
- Ha! Just kidding! Back to work. 
- This morning is mostly planning and scoping out future work. I’ve got a big backlog of features that I _want_ to add, but chose to delay implementing in favor of shipping some basic functioning prototypes to people. 
  - Sketched out a quick state diagram showing what I want to do next. 
- Worked on adding a timer to the project using ST CubeMX. 
  - This needs to be done carefully so that ST CubeMX doesn’t stomp on the existing project work. 
  - Did a lot of `git reset —hard` to test that generated code didn’t stomp on code I’d already written

# March 31, 2020

- Open question: how do you get a square wave out of an AD9834? 
  - There's a SQW output on the chip, but you'd need a clever way of designing it into the output stage of the AD9834.
  - Not clear to me yet how you'd do that, and select a square wave instead of the triangle/sine output from the AD9834.
- Spent today's workday writing and publishing a quickstart guide for bFunc - [check it out](http://cushychicken.github.io/bfunc-quickstart/)!

# March 30, 2020

- Time to ship some boards!! Here's what that procedure looks like:
  - Finish assembling (rework: GND connection!)
  - Program boards
  - Run through a quick outgoing QC procedure
    - Sine wave 
    - Set freq0
    - Set phase0
    - Triangle wave 
    - Square wave
  - ship boards to all of the beta testers!!! 
- Two problem boards found during OQC
  - One hasn't had its debug headers cut apart 
  - The other won't show up on a USB serial connection - Suspect it's a bad GND rework
- Here's me all gussied up to go to the post office in quarantine times!

![post_office_suit](../assets/images/post_office_suit.JPG)

# March 28, 2020

- Two things top of mind for me today: 

  - 1) A brainwave on how to deal with backspacing properly on Macs. 
  - 2) Correcting the slight offset in frequency observed between my scope’s 1kHz reference and the bFunc’s output.

- Dealing with backspacing properly

  - Main issue at this point is that my Mac terminal does not accurately reflect a backspace 

  - I think that’s because the Mac terminal is _not being sent a backspace character_

  - An easy way to test this is to just insert a USB transmit of a single backspace character in the switch statement case that handles DEL characters. 

  - Well, it was a good try, but it didn’t work. The line I added to the case statement was:

    `CDC_Transmit_FS((uint8_t *)"\b", 1);`

  - Any suggestions of how to get this working right with `screen`? 

- Now, onto the frequency output. 

  - I _think_ this is a case of just handling the casting between `unsigned int` and `float` types properly. 
  - I wrote a quick throwaway C program to test this:

```c
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>
#include <math.h>

int main(int argc, char *argv[]) {
  uint32_t  freqreg;
  float    frequency;

  printf("argv[1]: %s\n", argv[1]);
  frequency = (float)atoi(argv[1]);
  printf("frequency: %f\n", frequency);
  freqreg = (uint32_t)((pow(2.0, 28.0) / 16000000.0) * frequency);
  printf("freqreg: %d\n", freqreg);

  return 0;
}
```

- And it looks like this is the right solution - this matches my pen-and-paper calculations for an input of 1kHz:

```powershell
Nashs-MacBook-Pro:multiply_int_float nreilly$ gcc multiply.c
Nashs-MacBook-Pro:multiply_int_float nreilly$ ./a.out 1000
argv[1]: 1000
frequency: 1000.000000
freqreg: 16777
```

- So - let’s try plugging it into the code and seeing if it works!
  - …_and it does!!_ 
  - Well - sort of. There’s clearly a small offset between the 1kHz oscillator in the oscilloscope and the oscillator on board the bFunc. 
  - There are a few minor foibles in the product’s operation, and a few features in the chute, but this is an exciting milestone. _bFunc Rev1 is Feature Complete!!!_