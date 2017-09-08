---
layout: post
title:  "Dogecoin: part 2"
categories: old
---


I had a nice unobtrusive idea for my mining rig, which has become something of a chore: a cronjob to kill it at 6pm before I get home from work, and to reboot/restart the mining process at 8am to let me know it's on and running before I'm out the door.

Unfortunately, when I panicked that I lost all the shit I had on my computer before I installed Xubuntu and turned it into a mining rig (whatever that means..) -- well, I reinstalled Ubuntu in some irrational manner to retrieve what was lost.  Turns out, I backed it up on a hard drive.

Anyway, so now I have to do a fresh install.  And there were some issues with the drivers, so I'm documenting it here in case I forget and have to do it again.

Followed [this guide](http://www.cryptobadger.com/2013/04/build-a-litecoin-mining-rig-linux/) up until installing the fglrx drivers.  According to a forum thread, Ubuntu does not support those proprietary drivers, and I followed [this](http://wiki.cchtml.com/index.php/Ubuntu_Lucid_Installation_Guide#Open_Source_Drivers) to remove them and install the open source ones.

Update - Getting an error: libOpenCL.so.1: cannot open shared object file: No such file or directory

First trying to install python-pyopencl, which has dependencies I just installed.  Nope.  Now trying [this solution](http://ubuntuforums.org/archive/index.php/t-2118239.html).  Nope.  Hmm..  Another try to python-pyopencl.  I have held broken packages.  Gonna try rebooting.  Alright, so I realized I never actually installed the drivers.  Installed the AMD Catalyst driver (the less-gangster path) and got some output on my cgminer dry run.  Sweet.

Alright, got the mining script on my computer.  Haven't mucked around with the startup / kill aspect of it, but it's mining Litecoins on mine-litecoin.com.  Only took an hour.  I'd consider that a success.