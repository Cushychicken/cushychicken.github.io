---
layout: post
title: "Portfolio Project Ideas for the Embedded Developer"
summary: "songs to sing for your supper"
tags: ['electronics']
date: 2021-07-26

---

A friend of mine recently reached out about moving his career back towards embedded systems. He asked for a few project ideas that he could do to demonstrate his understanding of the field to potential employers. This is a real simple, first-pass list of project ideas that we came up with. 

I’d love to grow this over time as I come up with more. Let me know via email if you have any other great ideas for portfolio projects.  

- Write a UART shell/REPL that can control other parts of the MCU. Use it to talk to your board over a UART or a USB CDC connection. Use it to read/set pins, kick off SPI/I2C transactions, etc.
- Make a macropad for you computer. Instantiate the USB HID class. Understand how keypresses are interpreted by your laptop. 
- Write a driver for a SPI TFT screen. Write another I2C driver for a temp sensor. Make a thermometer/thermostat. Add some buttons for a UI interface that changes the display units from Fahrenheit to Celsius. 
- Write a low power application for a board setup. A nice example would be a temperature datalogger that you can leave outside that will take temperature over an extended period of time. See how long you can make it run from a single battery. Document what steps you tried to extend the device runtime. 
- Get an I2S DAC, an I2S ADC, and a reasonably powerful M7 or similar ARM processor. Stream music into the processor, and implement some digital filters, EQs, or other audio effects on the audio stream. Connect the board to some speakers. Wow friends and enemies alike with your modulations and whatnot. Pipe it into your computer's sound card and use Audacity to show the spectral effects on the raw waveforms.
- Write a bootloader that allows you to reprogram your MCU over SPI, I2C, or UART.
- Use some resistors and an MCU to implement a simple sigma-delta ADC. Write a filter chain that can convert the PDM output of the ADC to PCM. 
- Use some resistors and an MCU to implement a simple R-2R ladder DAC. Write a synthesis engine in C. Use it to generate sine, triangle, or square waves. 
- Write a driver for some hardware peripheral - either on-chip, like a SPI block or PWM peripheral, or off-chip, like an I2C or SPI device. Write an application layer for the same peripheral. Write a test suite between the two.
- Write a single phase brushed DC motor control application. (The DRV8801 is a great motor driver chip for this.) Implement a control loop that can drive the motor in constant velocity or constant position mode. Add a user interface on top of this that allows you to switch from constant velocity to constant position mode. Display angular offset to the user in CP mode, or tachometer data in CV mode.