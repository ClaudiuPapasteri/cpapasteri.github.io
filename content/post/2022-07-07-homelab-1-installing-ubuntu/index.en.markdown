---
title: 'HomeLab 1: Installing Ubuntu'
author: Claudiu C. Papasteri
date: '2022-07-07'
slug: homelab-1-installing-ubuntu
categories:
  - Ubuntu
  - HomeLab
  - psychlab
tags:
  - Ubuntu
  - HomeLab
  - psychlab
subtitle: ''
summary: ''
authors: []
lastmod: '2022-07-07T20:54:33+03:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []
editor_options: 
  markdown: 
    wrap: 72
---

### 1) Create a bootable USB stick for Ubuntu

Just follow the official recommendations

-   [on Windows (using
    Rufus)](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview)

-   [on
    Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview)

-   [on
    macOS](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview)

I installed an Ubuntu Desktop (Ubuntu 22.04 LTS) as the main difference
with Ubuntu Server is that the later does not have a desktop environment
(i.e. no GUI).

### 2) Install Ubuntu from USB stick

The bootable flash drive is ready to go. Plug the flash drive into the
computer when its powered off. Turn on the computer and use the hot key
to enter the BIOS of the motherboard (e.g. ESC for ASUS mobos like mine)
immediately after turning on. The hot key will vary depending on the
motherboard manufacturer, if you do not know the shortcut, look up the
motherboard make and model. Every BIOS is a little different depending
on the motherboard. In general, to install Ubuntu onto a new hard drive,
navigate to the motherboard's settings option and find a section titled
"Boot". Once you are in the Boot section, you should see a "priorities"
list which lists a certain number of Boot option priorities. Place the
USB hard drive in the "1st Boot" option, followed by the hard drive name
in the "2nd Boot" option. Some motherboards, like mine, aside from the
priorities list have a special feeld were you chose if you want to boot
from HDD or USB Flash drive. Others will automatically place the USB
drive before the hard drive. Once this is done, you can exit out of the
BIOS and restart the machine.

![](1.jpg)

![](2.jpg)

The machine will reboot and should open an Ubuntu installation screen
after a few moments. Go through the initial setup wizard and click the
defaults options. It is a good idea to connect to the Internet during
the installation and you may also want to check the non-default "install
third-party software for graphics and Wi-Fi" option. After you walk
through the setup wizard, you should have an installation of Ubuntu on
the hard drive.

After, don't forget to update and reboot:


```r
sudo apt update      
sudo apt upgrade
sudo reboot
```
