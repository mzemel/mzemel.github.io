---
layout: post
title:  "Swimming fish: part 2"
categories: old
---

This is more or less the current output of my research program.

[Bash script](https://github.com/mzemel/imager/blob/master/mcip.sh):

Take a picture of the fish; give it to the user to [crop](https://github.com/mzemel/imager/blob/master/src/crop.cpp)* for enhanced accuracy,
Implement a motor regiment (via [parallel port executables](https://github.com/mzemel/imager/tree/master/motor) I compiled),
Record video of the fish swimming for user-specific time (e.g., an hour),
Chops the video into images at a 1-second frame rate in an appropriately titled directory.
*I will make a separate entry about this C++ program I wrote.

[C++ program](https://github.com/mzemel/imager/blob/master/src/imager.cpp):


![fish1](http://i.imgur.com/A22lQhN.gif)
![fish2](http://i.imgur.com/u92b2Z6.png)

Load images chronologically into CImg objects,
Creates an array of standard deviations for each pixel of a generic image (as all are the same width and height),
Eliminates images where the fish has not moved significantly from the previous frame, using filters created from the standard deviations**,
Subtracts the background from the remaining images using fish-color approximations taken over the course of the program***,
Attempts to calculate the orientation of the fish by calculating its long axis, verified by the angle of the short axis (you can see the red cross-bar in some frames),
Exports orientation by time (taken from the image object’s name) to a plain text file (and to stdout),
Creates a time-lapse image of the fish over the hour.
**i.e. more permissive filters when the fish does not swim as much (in high flow regiments)

***created also by those dynamic filters; fish can be of any color

While this gif includes two poorly-approximated orientations, I have found my program to not deviate significantly from user-calculated orientation of images in GIMP (Zemel, M., and Zelick, R.  How do different lateral line ablative methods each affect flow orientation behavior in the suckermouth catfish, Hypostomus plecostomus?  Presented at Portland State University, Fall 2012.)