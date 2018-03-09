---
layout: post
title:  "Automated potentiometer"
categories: old
---

We use a variable power supply to provide power to a boat motor in our experiments.  The power supply has a manual potentiometer on it, used to control the voltage to the motor.  As my experiment lasts many hours and consists of changing the speed each hour, I decided it was worth automating the process.

![box](http://i.imgur.com/DuvEs8l.jpg)
![chip](http://i.imgur.com/BlosTUR.jpg)
![box2](http://i.imgur.com/prGVMCx.png)

I measured the resistances across the manual potentiometer for the five speeds we were using, and designed five boards to match these settings.  Each board contained a PNP transistor, a double-gated switch, diodes, two fixed resistors, and two trimming potentiometers (variable resistors.)

The computer connected to the bipolar junction transistor at the base and, when activated via parallel port programming, closed the switch between the yellow "in" and the red and green "out" wires.  These three wires took the place of the manual potentiometer, and the entire process was automated via my bash script described [here](http://mzemel.tumblr.com/post/47495059234/this-is-more-or-less-the-current-output-of-my).

A few months later, I built a device identical to this first one in order to incorporate more speeds.  See a problem?  While I made 10 units, the parallel port only has 8 pins that can be programmatically controlled.  Thus, I was forced to build a 'master' chip which would delegate control.  Using a similar set-up (computer control of transistor base) with many more switches, I was able to send a byte and set the motor to one of ten different speeds.

I'm relatively pleased with the appearance of the final product: it has a parallel port interface attached to the case, all transistor connections are hidden from view, and each of the two sub-units can be used functionally on their own (by only changing the byte sent from the computer.)

Again, all the code for this project (including the parallel port [executables](https://github.com/mzemel/imager/tree/master/motor) I wrote) is available on [GitHub](https://github.com/mzemel/imager).