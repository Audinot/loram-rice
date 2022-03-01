---
Title: Introduction
permalink: /
layout: default
---

# LORAM Rice

## Introduction
---
*Custom Arch+Sway, designed for the RPi Zero 2W with under 512mb RAM*

The challenge I set for myself was to build a useable desktop on a 
Raspberry Pi Zero 2w, as well as a satisfying rice. The keyword is 
that it must be *useable.* Unsatisfying performance was unacceptable. 
With only 512mb of RAM, only 418 of which show up as useable on htop, 
and no way to upgrade it, this means the installation and rice must be
lean. Here is a guide to getting your own $15 riced and ready for work!

Some assumptions: this guide is about building a desktop for work. Therefore
it's assumed you aren't running a headless RPi with SSH. I also chose to 
manually install Arch. Although you can install Manjaro easily using the 
Raspberry Pi Imager and end up with something similar, the spirit of the 
LORAM rice guide is to use as little space and resources as possible, while 
Manjaro does not have the same focus.

You will need:

- [x] Any Raspberry Pi, but this guide assumes you are using RPi02w.
- [x] A monitor. Although you can run a headless RPi, this guide is about desktops!
- [x] A keyboard.
- [x] Cables: HDMI, keyboard, power, and miniUSB-to-USB if required.

You will not need:

- [ ] A mouse. It's optional and this guide assumes you don't use one.

### Table of Contents

* [Chapter 1 ~ Installation and setup](./install.md)
* Chapter 2 ~ Network connection and basic tools
* Chapter 3 ~ Installing Sway
* Chapter 4 ~ Ricing
