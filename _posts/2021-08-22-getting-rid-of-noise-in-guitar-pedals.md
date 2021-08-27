---
layout: post
title: "Fixing Power Supply Noise in Guitar Pedals"
summary: "the only time a guitarist ever wants to kill the buzz"
tags: ['electronics']
date: 2021-08-22

---

Works with many audio circuits that *aren**’**t* guitar pedals, too. 

Note that I use the terms “wall wart” and “switcher” interchangeably here to describe a 9V AC/DC power supply. 

- Always diagnose the power supply as the root cause before diving into a fix. Swapping out your power supply with a 9V battery is without a doubt the fastest way to do this - gives your pedal the same amount of juice, but with no pesky switching noise. If the noise is gone when you switch to battery power, the wall wart is the source of the noise. (This advice assumes you’re working with a pedal that doesn’t have a built in switching regulator. Most pedals don’t, but some do - the Klon Centaur and the Keeley Katana are the two that pop to mind.) 
- Try additional rail capacitance. Adding more bulk caps to the main power rail, or replacing your existing caps for larger values, can reduce ripple which is making its way into your circuit.
- Try adding an inductor. A simple LC filter on your power rail can give good performance in the audio band, but reduce higher frequency noise from switchers that might be modulating into the audio band. An RC filter is acceptable in some cases too, where current draw is very low. 
- Consider adding a capacitance multiplier circuit to your bias rail. Great, cheap way to radically stabilize your bias points, which are low current draw. You can also try adding this to the circuit's 9V power rail. (Again, this is more appropriate the lower your pedal’s current draw is - I wouldn’t recommend for a circuit that draws more than 5mA.)
- Try a different wall wart. Not all wall warts are created equal. Some are noisy, some are not. There are zillions on Digikey. 
- Try a higher voltage wall wart (say 10V-12V), but add an LDO to step down the voltage to 9V. This is the "big hammer" approach. The LDO acts like a really hardcore active filter for switching noise.

I also have to give a shoutout to [Mr Carlson’s Super Probe](https://www.youtube.com/watch?v=uVkJqqZroN0), which I would have *killed* for when I was still actively developing audio equipment for a living. 

**Edit - 2021-08-27:** If guitar pedal electronics are your jam, I've done a few circuit analyses of some beloved pedal designs. if you haven't had a chance to look at the writeups for other pedals I've analyzed, those are available here:

<ul>
    {% for post in site.tags.ltspice-guitar-effects %}
    <li>
    	<a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endfor %}
</ul>

