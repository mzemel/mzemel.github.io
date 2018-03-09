---
layout: post
title:  "Deep Dream"
categories: games
---

# Deep Dream

Deep Dream is a library created at Google that uses a neural network to sequentially "interpret" an input, tweaking it until it matches a trained model.  For example, turning static into bananas:

![one](https://i.imgur.com/yzL0JPf.png)

Google has a [good article](https://research.googleblog.com/2015/06/inceptionism-going-deeper-into-neural.html) on what the tool is capable of.

What really captured my interest was how you can feed the same image into different models and get beautiful artwork styles.  Here's an example using abstract expressionism:

![two](https://i.imgur.com/QGqyKqW.png)

There are tons more [examples here.](https://photos.google.com/share/AF1QipPX0SCl7OzWilt9LnuQliattX4OUCj_8EP65_cTVnBmS1jnYgsGQAieQUc1VQWdgQ?key=aVBxWjhwSzg2RjJWLWRuVFBBZEN1d205bUdEMnhB)

# Deep Dream GIF

I went through some [install instruction](http://playittodeath.ru/how-to-install-caffe-on-mac-os-x-yosemite-10-10-4/), installing Caffe (a deep learning framework) and CUDA (to use my GPU), and started playing around with images.

As the process runs, it spits out an image at every Nth level of processing.  You can decide how many steps of processing you want.  For fun, I strung these images together using ImageMagick to form a GIF.

The results were very, very interesting:

![three](https://giant.gfycat.com/FlippantFluidAfricanaugurbuzzard.gif)

I used an image of a bison from my friend's trip to Yosemite.  Here, you can see the "dog slugs" - an artifact of the default model.  It quickly loses any semblance of the original image.

![four](https://giant.gfycat.com/DiligentPeskyGerenuk.gif)

This one starts from a picture of ivy in an abandoned building.  It evolves into cars, buildings, and trees.

DeepDream can be an art form in its own way, and people are using it to come up with some marvelous pieces.  Hope you enjoyed reading!