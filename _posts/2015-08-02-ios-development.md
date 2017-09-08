---
layout: post
title:  "iOS development"
categories: old
---

![iOS](http://i.imgur.com/ibAwIOI.png)

It was a long day today.  I worked on the iPhone app for about 9 hours, much of it spent troubleshooting, 'How not to make audio turn off when my app goes into the background.'

Interestingly, iOS has what is called an audio session - what I take to be the 'scope' of the audio.  Why they have sessions for individual aspects of an app seems odd at first, but makes sense given the large scale of what apps can do on the iOS architecture.

Where I left off a few weeks ago, I'd created an MPMoviePlayerController object from my [stream URL](http://stream.kpsu.org:1138/ios/ts/listen.m3u8), which seemed to be more capable of handling mp3-encoded streams.  I tried to replicate on my MacBook what I had going for segmentation of the stream (which uses ffmpeg to chop the [regular stream](http://stream.kpsu.org:8080/listen) into .ts files and produce an .m3u8 index file.)  I tried for about an hour to use the HTTP Live Streaming tools that Apple provides, namely their [mediastreamsegmenter](http://developer.apple.com/library/mac/#documentation/Darwin/Reference/Manpages/10.6/man1/mediastreamsegmenter.1.html), but it seems to require an mpeg-2 stream to also contain video.  The final error message I abandoned ship on was "average bit rate is 0.00 bits/sec."  Just about 64k less than I was hoping for.

I decided to scrap replicating the set-up and just went into school to start the iOS-compatible stream I'd set up on the streaming server a month ago.  To be precise, it's a local machine that autossh's into the web server.  For a while, I was using [Carson McDonald's GPL segmenter](https://github.com/carsonmcdonald/HTTP-Live-Video-Stream-Segmenter-and-Distributor) but kept running into difficulties and switched to ffmpeg and a less robust segmenter whose name I am blanking on (but will fill in later.)  At any rate, after 5 minutes I had a nice, lame-encoded mp3 stream with indexed .ts files and a publicly available .m3u8 file served by Apache.

The next part was, like I said, the framework to use in my XCode app: using MPMoviePlayerController to [play media in the background](http://developer.apple.com/library/ios/#qa/qa1668/_index.html).  I did eventually get it working using this document (with an hour lost by not listing "audio" as one option in an array in my info.plist file...) but experimented as well with [Matt Gallagher's Audiostreamer](https://github.com/mattgallagher/AudioStreamer).  It took a while to get the headers set up (as he is using XCode 3.x's file structure instead of XCode 4.0) and I learned a little more about Objective-C syntax (namely, that I really, really like the simplicity of Ruby...)  My conclusion after a while of trying his method is: it's really cool and comprehensive, but I should have used it from the start with my app to fully understand it.  When, after realizing my error, I got my MPMoviePlayerController approach working, it was smooth sailing.

The last problem I ran into was getting a volume slider bar linked to the stream.  Since I declared that the audio session was NOT going to be handled by my app (which would cause it to stop playing on locking the screen or exiting the app), I could not use an MPMusicPlayerController to link events from a self-created volume slider.  Instead, I had to create an [MPVolumeView](http://developer.apple.com/library/ios/#documentation/mediaplayer/reference/MPVolumeView_Class/Reference/Reference.html) to create a volume slider in a sub-view, and specify where I wanted it created geographically.  I can see this being an issue on an iPad and iPhone Retina, where the dimensions are different, so the next step will be finding a happy medium and hope the App Store doesn't reject me for imposing such proscribed placement of items.

Check out the current state of the app on [GitHub](https://github.com/mzemel/kpsu_iOS).