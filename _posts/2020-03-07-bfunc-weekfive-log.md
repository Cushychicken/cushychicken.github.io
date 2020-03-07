---

bg: "bfunc_proto.jpg"
layout: post
title: "bFunc - Project Journal - Week Five"
summary: "getting into the home stretch"
tags: ['electronics']
date: 2020-03-07
---

Here's the build log for week five of development for [the open source function generator I'm trying to prototype and build in time for for OSHWA 2020.](http://cushychicken.github.io/insane-oshwa-goals/) 

Each day's entry represents an hour's work per day - the hour before I leave for my job every weekday morning.

This is my log for the week ending Feb 28, 2020. Here's links to the prior weeks' logs:

* [Week One](http://cushychicken.github.io/bfunc-weekone-log/) 
* [Week Two](http://cushychicken.github.io/bfunc-weektwo-log/)
* [Week Three](http://cushychicken.github.io/bfunc-weekthree-log/)
* [Week Four](http://cushychicken.github.io/bfunc-weekfour-log/)

[The design doc for this project](http://cushychicken.github.io/bfunc-design-doc/) is available if you're into design documents/philosophy. 

All of my hardware and software source files are on GitHub. [Check 'em out!](https://github.com/Cushychicken/bfunc)

# Mar 6 2020

- Added Square Wave function 
  - Square wave amplitude is _vastly_ different than both the triangle and sine 
  - It’s because it’s a real digital output, not a current mode DAC
  - Will need to get clever on how to get around that if we ever add a buffer to this beast. 
- Starting a simpler USB CDC communication scheme rather than full parsing 
  - “s” sets a sine wave, “q” sets a square wave, “t” sets a triangle wave, and “i” sets to idle
  - This is pretty easy to implement with a “switch()” statement - just look at the first char of `UserRXBufferFS[0]`
- Regarding the DC offset I noticed when the AD9837 is idle:
  - Further reading in the AD9837 datasheet shows that the DAC has an “OFF” mode.
  - At some point we’ll want to try turning the DAC off and seeing if that eliminates the DC offset. 
  - This should be pretty simple to add into the IDLE state logic. 
- As an aside - macOS’s implementation of `screen` will trick you into thinking that something is connected when you try to open a USB device. It will even loop back characters for a few seconds before killing off the terminal. Bamboozled me into trying to debug a problem before realizing that my board wasn’t even connected to the computer via USB. Whoops. 

# Mar 5 2020

- Started a list of HW improvements for the next revision
- Finally plugged in and tested one of my reworked prototypes - largely good results! 
  - Talks over ST-Link debugger
  - Some bugginess here - I think that’s because I’m missing a bootstrap header (d’oh)
- Now it’s time to try and recompile my prior project work to target this new MCU. (Remind me why I changed MCUs again?)
  - I followed the procedure found in section 11.9 of STMicro doc [UM1718](https://www.st.com/content/ccc/resource/technical/document/user_manual/10/c5/1a/43/3a/70/43/7d/DM00104712.pdf/files/DM00104712.pdf/jcr:content/translations/en.DM00104712.pdf)
- Wow. The STM32Cube port worked!!
  - Needed to clear up a few compiler warnings to edit header files properly, but I was able flash code and get a sine and triangle wave out!
  - Next I need to implement a square wave now that this pesky DC coupling has been eliminated
- One thing I note on the output of the DDS - there's about a 300mV DC offset in the signal. 
  - Sort of suggests that there's a 1.5mA default current drive in the normal function generator output 
- I was in a boring 3 hour review meeting and my mind started to wander. As a matter of idle consideration during said meeting: what current output are most function generators specified to deliver? 
  - [Tek AFG1022](https://www.tek.com/datasheet/arbitrary-function-generator)
  - Mostly spec'd at 50 ohm load or open circuit 
    - DC
      - -5V to 5V output into 50 ohms
      - -10V to 10V output into high Z or open circuit
    - Amplitude - 50ohm load
      - 1mVpp to 10Vpp under 25MHz
      - 1mVpp to 5Vpp over 25MHz (up to spec'd range)
    - Amplitude - Open Ckt/High Z
      - 2mVpp to 20Vpp under 25MHz
      - 2mVpp to 10Vpp over 25MHz
  - FeelTech FY6900
    - DC offset 
      - -12V to 12V under 20MHz
      - -2.5V to 2.5V over 20MHz
    - Amplitude 
      - 1mVpp - 24Vpp under 5MHz
      - 1mVpp - 20Vpp from 5-10MHz
      - 1mVpp - 10Vpp from 10-20MHz
      - 1mVpp - 5Vpp over 20MHz

# Mar 4 2020

- Generate some parser logic for the state machine to chew on
  - Ideally something like: `<waveform> <freq> <phase>`
  - ex: `sine 25000 90` yields a sine wave at 25000Hz at 90 degrees phase offset
  - Ideally we should be able to send commands to the function generator from the computer serial port
    - ex: `sine 25000 90` yields a sine wave at 25000Hz with 90 deg phase offset
    - ex: `setfreq 26000` updates the frequency to 26000Hz 
    - ex: `set phase 45` updates the phase to 45 degrees
- First we need to get some actual serial data from the chip to ensure we’re getting it in and out the right way 
- It also seems like we need some kind of signal or semaphore to show the parser when new data is available
- We also need some buffer array to store all the chars incoming on the serial port - the VCP uses the same chunk of memory to store all incoming bytes 
- Also seems like we need to implement some sort of “end of frame” character in the buffer - otherwise, how will we know when we’re done parsing? Or when we’re done reading out from the USB buffer?
- A bunch of thought, not much code. 

# Mar 3 2020

- Here’s a second stab at getting USB up and running, now that I know how to un-brick the chip after flashing. First step is trying out [Shawn Hymel’s USB CDC guide for STM32](https://shawnhymel.com/1795/getting-started-with-stm32-nucleo-usb-virtual-com-port/) - let’s see how this works out. 
  - Success! We get the vaunted “Hello, World!” out of the virtual serial port!
  - Still many questions - especially why running this CDC example seems to lock up my SWD port. 
  - Future hardware will definitely include some DIP switches in lieu of headers/jumpers for the BOOT0/BOOT1 pins. They’re a bit painful to always wrestle on and off. 
- Also trying out the Damogran Labs loopback code. 
  - https://damogranlabs.com/2018/02/stm32-usb-cdc/
  - That gets loopback running too! Excellent! 
- Not much left to do at this point except start working on integrating the two systems. 
  - Started out by going into CubeMX and enabling the USB peripherals I need. 
  - That worked great, but apparently didn’t massage all the files that needed massaging. This error was the first hint something was off:

```powershell
In file included from Src/main.c:52:

Inc/usb_device.h:61:10: fatal error: usbd_def.h: No such file or directory

  61 | #include "usbd_def.h"

   |     ^~~~~~~~~~~~
   
   compilation terminated.
```

- After some judicious use of `find`, and a bit of side-by-side comparisons of demo projects, I ended up realizing that I was missing the right path to all the USB files generated by CubeMX. I ended up going into the makefile and adding the correct USB middleware paths. That got rid of all the compiler’s “I can’t find the files” complaints, but there are still some funky ones 

```powershell
/usr/local/Cellar/arm-gcc-bin/9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: build/main.o: in function `main':

/Users/nreilly/Desktop/projects/bfunc/sw_embedded/stm32_cube_bfunc/Src/main.c:186: undefined reference to `MX_USB_DEVICE_Init'

/usr/local/Cellar/arm-gcc-bin/9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: /Users/nreilly/Desktop/projects/bfunc/sw_embedded/stm32_cube_bfunc/Src/main.c:204: undefined reference to `CDC_Transmit_FS'

/usr/local/Cellar/arm-gcc-bin/9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: build/stm32f1xx_it.o: in function `USB_LP_CAN1_RX0_IRQHandler':

/Users/nreilly/Desktop/projects/bfunc/sw_embedded/stm32_cube_bfunc/Src/stm32f1xx_it.c:203: undefined reference to `HAL_PCD_IRQHandler'

/usr/local/Cellar/arm-gcc-bin/9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: /Users/nreilly/Desktop/projects/bfunc/sw_embedded/stm32_cube_bfunc/Src/stm32f1xx_it.c:207: undefined reference to `hpcd_USB_FS'

collect2: error: ld returned 1 exit status

make: *** [build/stm32_cube_bfunc.elf] Error 1
```

- Turns out this was more missing sources in the makefile. Moral of this story: if you generate additional CubeMX code later on in a project, *check out the `C_SOURCES` field of your makefile very carefully.*
- Alright!! I’m getting USB loopback code out of a virtual com port! 
- I pushed all of those goods to GitHub, as well as the BOM file I downloaded from CircuitHub. 
  - Should also probably push a PDF schematic as well. I hate when people don’t put PDF schematics on GitHub. 

# Mar 2 2020

- Getting down to the wire! Less than two weeks to OSHWA Summit. 
  - On the function generator side, I’m in great shape! Sine, square, triangle waves all chugging happily away. 
  - On the USB side: I’m in trouble. 😅
- On the bright side: I was able to de-brick my Blue Pill! Thanks to Jean Thomas for a little push in the right direction here: shuffling the BOOT0/BOOT1 pin strappings allows the chip to pull in boot configs from a different configuration - in this case, embedded SRAM. This keeps the chip from getting stuck in the state I have problems with: the one where it won’t respond to JTAG commands. 
- I’m still not sure where the problem is or how to fix it. 