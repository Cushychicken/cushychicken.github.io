---
layout: post
title: "NAND Flash: An Introduction"
date: 2015-06-27
---

## Abstract

This is the first in a series of posts I'm writing about NAND Flash data retention - why it's a problem, how to make it better, and how to make testable guarantees on those improvements. I've spent the better part of the last two years learning about NAND, and dealing with its limitations in embedded applications. I've spent a lot of that time debugging hardware, dealing with vendors, hacking through tech notes, talking to embedded software teams, and testing NAND components. My knowledge is assembled from an amalgam of these; until now, it's only existed in my head. I'm hoping that these posts can serve as a not-overwhelmingly-technical guide of the problem we're up against. I want you, the reader, to come out of this understanding weird behavior you see in your hardware, having a good handle on how to prevent the worst kind of failures, and understanding how to recover gracefully from more minor failures. 

## Introduction

NAND Flash is one of the most common ways to store data that needs to survive a power cycle of an embedded device. It's in your phone, it's in your computer, it's in your USB drive (supposing you still use a USB drive) - it's likely in just about every major consumer electronic product you own. NAND gained primacy as the nonvolatile storage mechanism of choice for a few simple reasons: it's cheap, it's small, it has a relatively low power consumption, and it's pretty easy to interface electrically. All of these things came at a price, however. Semiconductor companies, driven by Moore's law, have tirelessy struggled to miniaturize all things transistor based, and NAND is no exception. That miniaturization, however, is starting to run up against real physical boundaries that have real consequences for embedded designers - specifically, data integrity.

To understand what we're up against, we've got to start at the bottom, and work our way up. The first stop is the basic building block of NAND Flash - the transistor. 

## Transistor Theory - A Review

Transistors are the cornerstone of modern electronics. NAND is no exception to this fact. A quick device review of a field effect transistor: a transistor is basically a voltage-controlled resistance. By applying a voltage at the gate, you can change the resistance between the drain and source. In 
n-channel devices, a higher gate/source voltage means lower drain/source resistance. In p-channel devices, the opposite is true - higher gate/source voltage means higher drain/source resistance. (We'll focus on n-channel devices from here on out. You can assume that's what we're talking about 
unless stated otherwise.) 

<div align="center">
<img src="http://sub.allaboutcircuits.com/images/01058.png"/>
<p align="center"><em>Schematic symbols for FETs (Courtesy of AllAboutCircuits)</em></p>
</div>

A consequence of this changing resistance is that you can get a transistor to work as an electrical switch - apply a large enough voltage at the gate, and current can flow unimpeded between the source and drain. Remove that voltage, and no current can flow between them. This is a woefully incomplete description of all that a transistor can do, but for the time being, we can work with this assumption:

**A transistor is a switch that can be on or off, and that on/off state is how you store binary data.**

This is nothing new - it's the foundation of all digital logic. At the silicon level, things get more interesting in NAND Flash. 

## A Brief Blast of Device Physics

Below is how the schematic symbol above looks cut away in silicon (highly simplified, of course):

<div align="center">
<img src="https://upload.wikimedia.org/wikipedia/commons/7/79/Lateral_mosfet.svg"/>
<p align="center"><em>Simplified Side View of FET (Courtesy of Wikipedia)</em></p>
</div>

What's actually happening to turn on the connection between drain and source is a change in resistance of the transistor's body material. When there is a positive voltage on the control gate, there is a positive electrical field emanating from it.   This field attracts negatively charged electrons. If enough electrons from the body are attracted to the surface of the P-substrate, a conductive path (or "channel") forms between drain and source that's a lower resistance than the surrounding body material. 

(As an aside, this type of transistor is more accurately called a "field effect transistor", or FET for short. It's because the electric field of the gate is what affects the resistance between the source and drain.)

<div align="center">
<img src="http://m.eet.com/media/1175688/1212_mem_feat_keithley_fig1.jpg"/>
<p align="center"><em>Simplified Side View of Floating Gate FET (Courtesy of EETimes)</em></p>
</div>

So what's different in NAND? The answer - two gates! Note that these are labeled "control gate" (alternately referred to as the "word line") and a second gate - the "floating gate". Why is it floating? Because it is electrically isolated through oxides on all sides. How is this useful? Its isolation means it can store a charge. If it stores a charge, it produces a field. 

This charge (or lack thereof) is really where we're storing information in the system - it is the combination of the field from the control gate and the field in the floating gate that determines whether or not the transistor conducts. The transistor's on/off state is read to give you back data!

## Next Time and Further Reading
A floating gate is critical to storing data in NAND Flash. It's also the source of all problems with NAND Flash. My next post will go over, in detail, how the floating gate works, and how it can fail. 

If you'd like some further reading on the topic of transistors, [this article](http://www.explainthatstuff.com/howtransistorswork.html) from Explain That Stuff is a nice overview on transistors and some basic application examples. [The Wikipedia article on MOSFETs](https://en.wikipedia.org/wiki/MOSFET) is also a good resource regarding field effect transistors, but comprehensive to the point of daunting. 


