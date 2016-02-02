---
layout: post
title: Installing Android Studio on Ubuntu 64 bit
tags: ubuntu, android studio
---

Note to self: how to install Oracle JDK 8 and Android Studio on Ubuntu 14.04 64 bit:

When I installed Ubuntu using the x86_64 image, I realized that Android Studio and the Android SDK tools don't just install on the default Ubuntu 64-bit image, it's easier to use the 32-bit image, but I wanted to get it working anyway, so here's the steps for prosperity.

First install some dependencies needed by Android Studio and the Android SDK on Ubuntu x86_64:

    $ sudo apt-get install libindicator7 libappindicator1
    $ sudo apt-add-repository ppa:webupd8team/java
    $ sudo apt-get update
    $ sudo apt-get install oracle-java8-installer
    $ javac -version
    $ sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6

Download Android Studio using Chrome:

    $ google-chrome-stable
    $ cd ~/Downloads/
    $ unzip android-studio-ide-141.2456560-linux.zip
    $ mv android-studio ..
    $ cd ../android-studio/
    $ bin/studio.sh

Now install the latest Android SDK from within Android Studio and it should work!

I used a few StackOverflow posts to figure this out, like [this one](http://stackoverflow.com/questions/28804863/android-studio-how-to-install-android-platform-tools-on-ubuntu-14-04-64-bit)…
