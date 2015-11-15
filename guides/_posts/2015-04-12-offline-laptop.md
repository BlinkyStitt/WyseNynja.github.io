---
category: guides
date: 2015-04-12 14:00:00
layout: page
permalink: guides/offline-laptop
tags: security
title: "Bryan's Offline Laptop Setup"
subheading: "The Internet is a dangeours place"
---

Given the uncountable number of security problems becoming public everyday, keeping private things secure seems impossible, especially if you are ever going to plug your computer into the internet.

To help protect myself, I setup a laptop that will never connect to the Internet. However, even this setup has flaws.

This post is a work in progress.


# Buy

1. [Tin foil hat](http://en.wikipedia.org/wiki/Tin_foil_hat)
1. [Acer C720 Chromebook](http://www.amazon.com/mn/search/?_encoding=UTF8&camp=1789&creative=390957&field-keywords=chromebook%20acer%20c720&linkCode=ur2&tag=stitth06-20&url=search-alias%3Daps&linkId=MS6KQH4YOWZHFO4C)
1. [USB flash drive](http://www.amazon.com/b/?_encoding=UTF8&ajr=0&camp=1789&creative=390957&linkCode=ur2&node=3151491&tag=stitth06-20&linkId=CYJSSRATO647ERMJ)


# Download

1. [HugeGreenBug's Lubuntu 14.04 64bit for Acer C720](https://www.distroshare.com/distros/get/16/)
1. [Virtualbox](https://www.virtualbox.org/)
1. [Lubuntu 14.04 64bit Minimal](https://help.ubuntu.com/community/Installation/MinimalCD)


# Donate

1. Donate bitcoin to John Lewis: [16xnfmKiwAwYyAUh25sJ7rQeZdMn4WksRV](
bitcoin:16xnfmKiwAwYyAUh25sJ7rQeZdMn4WksRV)
1. Donate bitcoin to Huge Green Bug: [18Kty1XFh7pwwT8sbhNzmGuzsmgeFwUsKS](bitcoin:18Kty1XFh7pwwT8sbhNzmGuzsmgeFwUsKS)


# Setup a Virtual Machine for Updating the Chromebook

On your normal Laptop:

1. Open Virtualbox
1. Create a new box named "Offline Lubuntu 14.04 Updater"
    * 2048 MB of RAM
    * Create a VDI, dynamically allocated, 80 GB drive
1. Open Settings > Storage
1. Select the CD/DVD Drive and select the Lubuntu minimal ISO
1. Click "OK"
1. Click "Start"
1. Follow the prompts to install Lubuntu
    * No need for full disk encryption here. Your host system is already encrypted, right?!
1. Install [APTonCD](http://aptoncd.sourceforge.net/) with `apt-get install aptoncd`
1. Install any other packages you need with `sudo apt-get install ...`
    * common tools include: `git` `vim` `gnupg2`
1. Create an ISO with APTonCD and save it to the USB flash drive
1. Eject and unplug the USB drive


# Setup the Chromebook

1. Put on your tin foil hat
1. Remove the write protect screw from the Chromebook
1. Run [John Lewis' script](https://johnlewis.ie/custom-chromebook-firmware/rom-download/) to make booting linux on the Chromebook easy.
1. Put the write protect screw back
1. Install HugeGreenBug's Lubuntu on the Chromebook (Follow the instructions on the download page)
    * Enable full disk encryption
    * Disable bluetooth and wifi
    * Firewall all inbound and outbound traffic
    * Change apt sources to install from /mnt/APTonCD
1. Plug in the USB drive with the ISO from APTonCD
1. Double click the ISO to mount it
1. `sudo apt-get update && sudo apt-get upgrade`
1. If you installed any extra tools on your VM, `sudo apt-get install` them


# Updating and Installing New Packages on the Chromebook

In your "Offline Lubuntu 14.04 Updater" VM:

1. Plug in the USB drive that you will use to transfer the ISO from APTonCD
1. `sudo apt-get update && sudo apt-get upgrade`
1. `sudo apt-get install ...`
    * Install any packages that you want on your offline system
1. Create an ISO with APTonCD and save it to the USB drive
1. Eject and unplug the USB drive

On your Chromebook:

1. Plug in the USB drive with the ISO from APTonCD
1. Double click the ISO to mount it
1. `sudo apt-get update && sudo apt-get upgrade`
1. `sudo apt-get install ...`
    * Install the same packages that you installed above
1. Eject the ISO and the USB drive


# Todo

1. More detailed steps for APTonCD
1. HGB's Lubuntu comes with a couple of PPAs. How should we get updates from them to the offline system
1. Figure out how to negate BadUSB attacks
1. Build the ROM from source
1. Build the Lubuntu ISO from source
1. Install a larger SSD
1. Install personal dotfiles
1. Figure out how to use APTonCD's meta package. When I tried it the first time it tried to install some extra things since the VM had a couple extra things that I didn't want to bring over


# Notes

1. There are tons of Linux distros to choose from. I chose the Acer C720 and HugeGreenBug's Lubuntu  because he and some other people like John Lewis have already done the work to get everything working smoothly
1. The fixes for the Chromebook make HGB's ISO unstable under Virtualbox

