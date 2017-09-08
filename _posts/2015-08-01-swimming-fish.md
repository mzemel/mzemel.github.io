---
layout: post
title:  "Swimming fish"
categories: old
---

![fish](http://68.media.tumblr.com/bdd905ca09682627d36ae64eaf8895b9/tumblr_mkt6uzEuzL1snwexao1_400.gif)

I use the [CImg](https://sourceforge.net/projects/cimg/) library in my research.  Part of my program entails looping through images and extracting pixel values, calculating standard deviations, and applying dynamic filters that subtract the background so I can calculate fish orientation.

This silly set of images, after subtracting the background, replace the pixels of the fish with corresponding pixels of a simple fractal.