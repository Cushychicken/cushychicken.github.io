---
bg: "/electronics_thermals.jpg"
layout: page
title: "Resume"
crawlertitle: "Professional Work, By Year"
permalink: /resume/
summary: "what would you say you do here?"
active: Resume
---

This is a working resume of sorts. It's a more fine-grained version of my professional accomplishments than the traditional one-page document. These are occasionally supplemented by blog posts, where appropriate, to illustrate what I've done, or what I learned from a project. 

They go in reverse order, and trail what I'm working on now by about a year, out of respect for my current employer. 

I'd be happy to chat about any of these topics in greater depth via Zoom, email, or in person!

# 2021

## Independent Consultant
* Started working as an independent consultant on electronics, embedded systems, and firmware development

## RTLjobs.com
* Created a job website exclusively for users of hardware description languages like VHDL, Verilog, and SystemVerilog - view live at [www.RTLjobs.com](https://www.rtljobs.com)!
* Wrote custom scraper/indexer in Python to crawl job postings from employers who need RTL skillset
* Wrote custom ETL pipeline to collect analytics on hiring trends among employers, hot geographies for RTL jobs, skillsets of interest 
* Developed and deployed website using Flask and a minor amount of HTML/CSS
* Built mailing list of FPGA/RTL engineers 

## ReportCardWriter.com
* Conducted user interviews on report card comment software
* Built additional software features 
	* Pronoun scrubbing algorithms
	* Comment template database at ReportCardComments.com
* Built email list and content marketing pipeline for teachers to improve ReportCardWriter's search rankings

## Root.ai -> AppHarvest
* Provided technical diligence on Root.ai's electrical systems to the AppHarvest acquisition team
* Redesigned Virgo main system controller board after identifying numerous issues with its predecessor
	* Responsible for all SCH design; supervised PCB layout with contractor
	* Wrote all test firmware for board bringup
	* Deployed board upgrade on in-house and field robots with field service team
* Continued to lead the Electrical and Embedded Systems team  
	* Grew the team from myself as solo contributor to four people
	* Hired, managed, and developed individual contributors with a range of skillsets: firmware development, circuit design, cable design, and system integration


# 2020

## Sonos 
* Finalized development of Titan, the Sonos / IKEA collaboration that launched as the SYMFONISK Picture Frame.
* Worked as Rotational Product Manager with the Architectural Sound team. 
	* Conducted research on solutions for the architectural product space
	* Held user interviews with custom integrators/installers on new product concepts

## ReportCardWriter.com
* Started a small, independent software company that helps teachers write report card comments more quickly. [Check us out!](https://reportcardwriter.com)
* Prototyped a first-run Python application/GUI for initial testing.
* Conducted dozens of user interviews to gather feedback on first prototype.
* Created landing page and marketing material to test broader response.
* Rewrote application with clearer user interface based on user feedback.
* Acquired a small, steadily growing user base through word of mouth. 


## Root.ai
* Joined [Root.ai](https://root-ai.com/) as a Principal Electrical Engineer.
	* Architected the electrical system of the Virgo Mk.V and Mk.VI robots
	* Identified and resolved issues around vibration-based sensing; redesigned to use more reliable optical beam-break sensors instead
	* Managed the company's transition from EAGLE to KiCAD 

* Founded and led the Electrical and Embedded Systems (ESYS) team. 
	* Managed and planned team workload across releases. 

# 2019

## Sonos
* Launched Neptune publicly as the Sonos Port
    * Worked with external power supply vendor on compliance and voltage sag issues 
    * Finalized design docs and test plans for manufacturing ramp
* Evaluation/bringup of the Amlogic A113L processor
    * Schematic capture 
    * Subsystem design 
    * Directed and supervised layout
    * Build out of detailed evaluation platform 
    * Bringup of boards 
* Evaluation of the A113L voice activity detection peripheral 
    * Created model of VAD peripheral's DSP pipeline in Python
    * Verified performance of Python model against vendor's Matlab DSP model. 
    * Integrated VAD model into Sonos Voice Quality test system; tested for regression against existing voice metrics. 
* Started work on Titan project 
    * Modular CPU concept for easy reuse in multiple products 
    * Schematic capture, testplan development, specifications work to ensure proper operation 
    * Redesigned amp circuitry to improve click/pop
    * Debugged power routing to eliminate wifi noise coupling into amps
    * Completed bringup and system integration of the MT7613 wifi radio into the Titan board

## Consulting
* Served as scribe for Innovation Discovery Event tech transfer sessions at:
    * Princeton Plasma Physics Laboratory; Princeton, NJ
    * US Department of Agriculture; Beltsville, MD

# 2018
* Digital Lead for Sonos Amp (Codename: Hideout)
    * Shipped early and under budget - Sonos first!
    * Root caused several breaking issues around memory, compliance, auth circuitry
    * Owner of digital protection circuitry in amp - implemented subsystem communications and protection circuitry using dsPIC MCU
* HDMI Compliance Issues
    * Discovered breaking compliance issues for multiple products implementing Mediatek MT8521P processor
    * Prevented serious compliance problem in product shipments - fixed with SW update after diagnosis
* Lead EE for Neptune project (later shipped as Sonos Port) 
    * Owner of all electrical assemblies
    * Responsible for schematic, PCB, testplan development
    * Designed and spec'd low-noise D/A chain with -109dB THD+N (>20dB THD+N/SNR improvement over prior system)
    * Designed and spec'd 12V trigger circuitry

# 2017
* Digital Lead of Paramount project - CPU/Radio Redesign of Sonos Connect
	* Diagnosed and resolved radiated emissions issues in Ethernet chain
	* Resolved audio click/pop issues - traced to poorly documented legacy hardware
	* Shipped on time for $13.50 savings per unit, ~$3.4million annual savings
* Implemented PDM microphone demodualtor/DSP pipeline in Verilog
	* Implemented CIC filtering and polyphase lowpass FIR filter to convert PDM mic output to I2S
	* Integrated PDM decimation filter in Lattice ICE40 FPGA
* Digital Lead - Hideout - ConnectAmp Refresh
	* Owner of PCB assembly for CPU/RF circuitry
* Owner of digital protection circuitry in amp
    * Designed/architected dsPIC33 MCU as mixed/signal amp controller
    * Responsible for all abstraction of amp subsystem
    * Implemented subsystem communications and DSP protection functions
* Co-designed custom class D amplifier featuring novel modulator technology from Qualcomm

# 2016 
* Started work on the Paramount project - redesigned Sonos Connect for lower cost
	* Owned architectural decisions, board bringup, prototype testing, thermal budgeting
	* Developed crossfunctional sepcs for test fixturing, diagnostic SW development, and system integration
* Prototyped first Sonos board with HDMI as feature 
	* Prototype board featuring the NXP iMX6DualLite
	* Did initial bringup of Mediatek MT7615 radio
* Completed firmware bringup of wireless power transfer prototype
	* Initial bringup of system microcontroller	
	* Developed proof-of-life firmware for diagnostic shakeout

# 2015
* Managed the early life failure program for the Sonos Boost; identified and resolved issues not discovered in production ramp 
* Redesigned CPU clock network of Sonos ConnectAmp CPU as part of a memory upgrade
* Developed continuous qualification program for streamlining component second sourcing 

# 2014
* Debugged line-halting amplifier overcurrent issue in Sonos Playbar
* Established second source qualification procedure for DRAM and NAND Flash memories
* Discovered widespread bug in Spansion NAND Flash chips; received a very nice thank you note from the CEO of Spansion

# 2013
* Micron
    * Data-mined manufacturing logs for analytic trends on failed DRAM modules
	* Developed DRAM performance monitoring software for Intel Sandy Bridge series processors
* Sonos 
	* Resolved line-down issues regarding Flash and DRAM memory supply
	* Standardized NAND Flash qualification procedures

