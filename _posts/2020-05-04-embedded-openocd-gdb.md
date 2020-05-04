---
bg: "archerfish_hirez.jpg"
layout: post
title: "GDB + OpenOCD Setup - Notes for Later"
summary: "i guarantee you'll need these again"
tags: ['programming']
date: 2020-05-04

---

Here's some instructions I'm writing to my future self on setting up GDB and OpenOCD. I don't set up GDB and OpenOCD too frequently, so I want to leave myself a few breadcrumbs for the next time it happens. I probably wouldn't have bothered in the first place if I hadn't had issues connecting to the OpenOCD instance that `st-util` hosts on this new Ubuntu machine. [Lots of issues with flash loader support](https://github.com/stlink-org/stlink/issues/356) in the OSSW version of STLink, apparently.

In any case - here are some instructions for future me on setting up OpenOCD. I'm running Ubuntu 20.04 here, but it looks like results don't vary widely across Linux distros. 

# Configure OpenOCD first

OpenOCD is the right place to start your setup. It's what manages the physical connection between your computer and your microcontroller. You'll want to do the typical installation song and dance:

```sh
$ sudo apt-get install openocd
```

And then you'll want to start an `openocd.cfg` file in your project directory. 

You'll want to leverage some of the built-in config files that are bundled with OpenOCD. As per the [project setup guide](http://openocd.org/doc/html/OpenOCD-Project-Setup.html#OpenOCD-Project-Setup), these files are located at `/usr/share/openocd/scripts` on most Linux systems, and that's where I was able to find them: 

```bash
nreilly@banoodler:~/projects/bfunc/sw_embedded/bfunc_rev1$ ll /usr/share/openocd/scripts/
total 88
drwxr-xr-x 11 root root  4096 May  4 06:33 ./
drwxr-xr-x  5 root root  4096 May  4 06:33 ../
-rw-r--r--  1 root root  1601 Feb 10 13:53 bitsbytes.tcl
drwxr-xr-x  2 root root 20480 May  4 06:33 board/
drwxr-xr-x  5 root root  4096 May  4 06:33 chip/
drwxr-xr-x  2 root root  4096 May  4 06:33 cpld/
drwxr-xr-x  3 root root  4096 May  4 06:33 cpu/
drwxr-xr-x  2 root root  4096 May  4 06:33 fpga/
drwxr-xr-x  3 root root  4096 May  4 06:33 interface/
-rw-r--r--  1 root root   787 Feb 10 13:53 mem_helper.tcl
-rw-r--r--  1 root root  4563 Feb 10 13:53 memory.tcl
-rw-r--r--  1 root root  1648 Feb 10 13:53 mmr_helpers.tcl
drwxr-xr-x  2 root root 12288 May  4 06:33 target/
drwxr-xr-x  2 root root  4096 May  4 06:33 test/
drwxr-xr-x  2 root root  4096 May  4 06:33 tools/
```

Since we're trying to establish a physical connection first thing, we want to first define an _interface_ to the debugger and the microcontroller, so naturally, the `interfaces` directory should be our first stop:

```bash
nreilly@banoodler:~$ ll /usr/share/openocd/scripts/interface/
total 136
drwxr-xr-x  3 root root 4096 May  4 06:33 ./
drwxr-xr-x 11 root root 4096 May  4 06:33 ../
-rw-r--r--  1 root root  190 Feb 10 13:53 altera-usb-blaster2.cfg
-rw-r--r--  1 root root  215 Feb 10 13:53 altera-usb-blaster.cfg
-rw-r--r--  1 root root   96 Feb 10 13:53 arm-jtag-ew.cfg
-rw-r--r--  1 root root  102 Feb 10 13:53 at91rm9200.cfg
-rw-r--r--  1 root root  484 Feb 10 13:53 buspirate.cfg
-rw-r--r--  1 root root  195 Feb 10 13:53 calao-usb-a9260.cfg
-rw-r--r--  1 root root  115 Feb 10 13:53 chameleon.cfg
-rw-r--r--  1 root root  218 Feb 10 13:53 cmsis-dap.cfg
-rw-r--r--  1 root root   63 Feb 10 13:53 dummy.cfg
-rw-r--r--  1 root root   75 Feb 10 13:53 estick.cfg
-rw-r--r--  1 root root  331 Feb 10 13:53 flashlink.cfg
drwxr-xr-x  2 root root 4096 May  4 06:33 ftdi/
-rw-r--r--  1 root root  268 Feb 10 13:53 jlink.cfg
-rw-r--r--  1 root root  354 Feb 10 13:53 jtag_vpi.cfg
-rw-r--r--  1 root root  258 Feb 10 13:53 nds32-aice.cfg
-rw-r--r--  1 root root   84 Feb 10 13:53 opendous.cfg
-rw-r--r--  1 root root   96 Feb 10 13:53 openjtag.cfg
-rw-r--r--  1 root root  114 Feb 10 13:53 osbdm.cfg
-rw-r--r--  1 root root  371 Feb 10 13:53 parport.cfg
-rw-r--r--  1 root root  296 Feb 10 13:53 parport_dlc5.cfg
-rw-r--r--  1 root root 1183 Feb 10 13:53 raspberrypi2-native.cfg
-rw-r--r--  1 root root  991 Feb 10 13:53 raspberrypi-native.cfg
-rw-r--r--  1 root root  155 Feb 10 13:53 rlink.cfg
-rw-r--r--  1 root root  156 Feb 10 13:53 stlink-v1.cfg
-rw-r--r--  1 root root  488 Feb 10 13:53 stlink-v2-1.cfg
-rw-r--r--  1 root root  484 Feb 10 13:53 stlink-v2.cfg
-rw-r--r--  1 root root  554 Feb 10 13:53 sysfsgpio-raspberrypi.cfg
-rw-r--r--  1 root root  349 Feb 10 13:53 ti-icdi.cfg
-rw-r--r--  1 root root  162 Feb 10 13:53 ulink.cfg
-rw-r--r--  1 root root 1600 Feb 10 13:53 usb-jtag.cfg
-rw-r--r--  1 root root  221 Feb 10 13:53 usbprog.cfg
-rw-r--r--  1 root root   82 Feb 10 13:53 vsllink.cfg
```

I'm using an ST-Link V2 debug probe, so I'll want the config file that maps to that probe: `stlink-v2.cfg`. I add this as the first line to my `openocd.cfg` file:

```bash
source [find interface/stlink-v2.cfg]
```

Then I call `openocd` from the directory in which the file is locate. `openocd` pulls in its configs from `openocd.cfg`, and then promptly complains that additional settings are needed:

```bash
nreilly@banoodler:~/projects/bfunc/sw_embedded/bfunc_rev1$ openocd 
Open On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Error: An adapter speed is not selected in the init script. Insert a call to adapter_khz or jtag_rclk to proceed.

```

This is no big deal - `openocd` needs some more info about the physical connection between the debugger and the target microcontroller. This is as simple as adding another line for the target MCU config, which you can find in the `/usr/share/openocd/scripts/target` directory. I'm using an STM32F072 microcontroller, so I chose the `stm32f0x.cfg` file, and added it to `openocd.cfg`:

```bash
source [find interface/stlink-v2.cfg]

source [find target/stm32f0x.cfg]
```

Firing this up yields us the result we're looking for - some debugger info, API versions, breakpoint info from the target chip, and even the voltage detected by the debugger. We're in business!

```bash
nreilly@banoodler:~/projects/bfunc/sw_embedded/bfunc_rev1$ openocd
Open On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
adapter speed: 1000 kHz
adapter_nsrst_delay: 100
none separate
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : clock speed 950 kHz
Info : STLINK v2 JTAG v29 API v2 SWIM v7 VID 0x0483 PID 0x3748
Info : using stlink api v2
Info : Target voltage: 3.237616
Info : stm32f0x.cpu: hardware has 4 breakpoints, 2 watchpoints


```

It's worth pointing out that there's a *ton* of useful information in `stm32f0x.cfg`. It's worth a quick perusal so you can see what's happening at the debug protocol level. It will make you say a quiet "thank you" to the apps engineer who got it cranking for you.  

# Use gdb-multiarch

I originally tried installing `arm-none-eabi-gdb`, but that's apparently no longer needed thanks to `gdb-multiarch`. I was able to install `gdb-multiarch` the typical way:

```
$ sudo apt-get install gdb-multiarch
```

And then start a quick `.gdbinit` script in the same project directory in which I stored `openocd.cfg`. 

```bash
file "./build/bfunc_rev1.elf"

target extended-remote :3333

load
break main

continue
```

This takes care of a few things for you:

* `file "./build/bfunc_rev1.elf"` loads up your symbol table, and helps you use friendly, human-readable function names when you're debugging.
* `target extended-remote :3333` tells `gdb-multiarch` that it's connecting to a different CPU to debug. `extended-remote` is a particular operating mode that allows GDB some flexibility in connecting to, and disconnecting from, the remote machine. The `:3333` just specifies
* `load` takes care of loading the various memory sections of the target into GDB's memory. 
* `break main` sets a breakpoint at the `main` entry point of your C program. This can cause you some grief if you are trying to look at the early boot process that happens before `main` is entered. I'll need to figure out a clever way around this if I ever need to do bootloader stuff again.