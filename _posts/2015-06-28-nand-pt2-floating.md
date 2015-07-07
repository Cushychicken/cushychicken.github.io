---
layout: post
title: "NAND Flash: Floating Gates"
date: 2015-07-06
---

## Introduction
Last time, we talked about transistors, the central component of NAND Flash, and how transistors in NAND Flash are slightly different than other FET devices. This post is going to focus exclusively on that particular difference - the floating gate. It contains the key to explaining some fundamental constraints of NAND Flash that shape the architecture of the overall systems. 

## Float Like a Butterfly
For a quick recap of the last post, let's take a look again at a simplified floating gate transistor - note the floating gate wedged between the two non-conducting layers of tunnel oxide and ONO dielectric.

<div align="center">
<img src="http://m.eet.com/media/1175688/1212_mem_feat_keithley_fig1.jpg"/>
<p align="center"><em>Simplified Side View of Floating Gate FET (Courtesy of EETimes)</em></p>
</div>

Since this is two-dimensional, you can't see one crucial fact about the floating gate - it is completely surrounded on all sides by non-conducting materials. Think of this image as a simple two-dimensional cutaway - if you were to slice a floating gate transistor in half, this is what you would see at the side view. The name "floating gate" implies the effect of this: it is a "floating" node, with no electrical connection to anything else in the circuit. On face, it doesn't seem super useful - how can we store electrons in this area if current can't flow there?

## The Bridge and Tunnel Crowd
Electrons, with subatomic size and mass, are subject to a number of fascinating quantum effects that aren't observable in macroscopic manner. This is due to the fact that electrons are subject to the wave/particle duality - you can describe and explain their behavior and motion equally well as a particle or a wave, depending on the circumstance. The full list of these effects is enormous, and studying and quantifying them has been the subject of numerous careers, some of them Nobel prize-winning. The one we want to focus on in the context of NAND Flash is a corollary of the [Heisenberg uncertainty principle](https://en.wikipedia.org/wiki/Uncertainty_principle), which states that the position and momentum of an electron can't be precisely known. Instead, you have to consider an electron's position as a probability distribution, or wavefunction. Essentially, you can't say _exactly_ where an electron is. You can only have a pretty good guess as to _where it might be_. The simplest way to visualize this is to restrict it to a really simple mathematical case - in this instance, it's a single electron, trapped in a well of infinite potential energy. (You can also read that as "stuck between two really high voltages".) We'll also restrict movement of said electron to a single dimension and say it can only move in the X dimension, or from left to right in the image below. 

<div align="center">
<img src="https://upload.wikimedia.org/wikipedia/commons/1/13/Infinite_potential_well-en.svg"/>
<p align="center"><em>A potential well (Courtesy of Wikipedia)</em></p>
</div>

We know that the trapped electron is going to be somewhere between `x = 0` and `x = L` in the figure above. We don't know exactly where, but since we know that the potential at either end is really, really high, we can say that the most likely location of the electron is where the potential is lowest. In this case, that's around the center, or `x = (L/2)`. The wavefunction showing this is the bottom diagram in the figure below, on the left side - you can see a nice probability distribution going from a probablility of zero at `x = 0`, peaking at `x = (L/2)`, and gently sloping back to zero probablility at `x = L`. 

<div align="center">
<img src="http://labman.phys.utk.edu/phys222core/modules/m10/images/1d_pro2.gif"/>
<p align="center"><em>Wavefunctions in Infinite and Finite Potential Wells</em></p>
</div>

This isn't exactly how things are in the real world. Try as we might, humanity is not capable of generating infinite potential wells - we can only generate really, really high voltages to contain electrons. This means that the wavefunction of an electron in a real potential well looks a little more like the figure on the bottom right - still peaking at the center, where potential is lowest. More importantly, it shows that the wavefunction is actually capable of existing outside of this region. It shows that there is a chance for electrons to exist outside of the well. 

It turns out that this fact leads to some extraordinary consequences - especially, since at the atomic level, you can model matter as a potential that repels electrons. Since the graph above clearly shows that an electron can exist at a high potential area, one can actually model isolated conductors within non-conducting regions as wells that electrons can sink into. 

<div align="center">
<img src="http://www.nature.com/nmat/journal/v4/n2/images/nmat1307-f1.gif"/>
<p align="center"><em>Forcing electrons into isolated conductors through tunnelling</em></p>
</div>

By raising the voltage on one side of the well, we're doing the electrical equivalent of tipping over a bucket. The end result is electrons "spilling" into the well. 

Clearly, this isn't an accurate metaphor - electrons aren't like water. And, obviously, there's no current flowing to this isolated region. So how are we moving the electrons into the floating gate? 

This is where things get interesting. The electrons aren't _flowing_ into the floating gate - they are _teleporting_ there! This process is known as Fowler-Nordheim tunnelling, and is the basis for how all Flash memory works. The more you raise the voltage on one region, the more the voltage in an isolated region starts to look like a well, as described above. Raise the voltage enough, and the the likelihood of electrons tunnelling through the gate conductor becomes high enough that it actually starts to happen, and electrons jump into the floating gate. You can have the reverse effect by simply reversing the voltage - electrons will tunnel back out of that trapped oxide layer and back into the bulk silicon!

<div align="center">
<img src="http://abyss.uoregon.edu/~js/images/quantum_tunneling.gif"/>
<p align="center"><em>Another Useful Depiction of Quantum Tunnelling</em></p>
</div>

## Wrapup
This is really scratching the surface of what's going on in a NAND array; the device physics at play are quite complex. The tunnelling phenomenon itself is pretty amazing. In addition to its application to NAND Flash, it's also the basis for the technology behind electron microscopes, which are how we get high quality images of insects, cells, and other atoms!

In the next post, we'll start to go up a level from the floating gate and move into some architectural considerations of how NAND memory is organized, and how tunnelling affects that. A little knowledge of tunnelling will go a long way - no more device physics in the next article!

##Further Reading
The [Wikipedia article on Fowler-Nordheim tunnelling](https://en.wikipedia.org/wiki/Field_electron_emission#Fowler.E2.80.93Nordheim_tunneling), like most Wikipedia articles, is comprehensive to the point of dense, but is probably the most complete treatment on the subject.

[This page from Physics of the Universe](http://www.physicsoftheuniverse.com/topics_quantum_uncertainty.html) also has a nice overview of how quantum tunnelling works, in general terms. 