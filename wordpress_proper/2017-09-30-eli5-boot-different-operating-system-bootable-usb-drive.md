---
id: 752
title: 'ELI5: How to boot a different operating system with a bootable USB drive'
date: 2017-09-30T10:58:52+00:00
author: Lars Veelaert
layout: post
guid: https://demgeeks.com/?p=752
permalink: /eli5-boot-different-operating-system-bootable-usb-drive/
tdc_dirty_content:
  - "1"
post_views_count:
  - "66"
image: /wp-content/uploads/2017/09/7-2-Boot-Device-Network-Menu.gif
categories:
  - ELI5
tags:
  - bios
  - boot order
  - development
  - hardware
  - linux
  - mac
  - mac osx
  - macos
  - os
  - productivity
  - usb
  - windows
---
Most devices come with a operating system (Windows, MacOs, Linux, Android, IOS, &#8230; ) already installed on your hard drive. That means that when the device boots, it has to know where to look. Various systems exist for this specific task, but the most common one is [BIOS](https://nl.wikipedia.org/wiki/BIOS). A small, programmed chip on your motherboard checks each port where a storage medium (Hard drive, CD, Floppy, USB, &#8230;) could be plugged in and reads the first bits of the disk. If it finds one with instructions on how to boot that storage device, it will do so. The order in which all the devices get checked can be changed (more on this later).

If a storage medium with a bootable signature (found in something called an [MBR](https://nl.wikipedia.org/wiki/Master_boot_record)) is found (like mentioned above). The rest of that first portion of data will tell you how the drive is partitioned, and on which location your operating system is located. Your CPU can load in your operating system and your device will boot your operating system.

There are two things that need to happen if you want to boot another storage device with another operating system:
  
1. Your [BIOS](https://nl.wikipedia.org/wiki/BIOS) has to check your storage device first. So it does not boot the normal one first.
  
2. That storage device has to have the bootable signature with instructions on how to boot your new operating systems.

## Changing the BIOS settings

When your computer starts, it will often display some white text with keys like (F11, DEL) next to it. If you react before the computer starts to boot, you have options to change the settings or choose the device you want to boot. An example BIOS boot screen is this:

<img class="alignnone wp-image-753 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=600%2C450&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=600%2C450&ssl=1 600w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=300%2C225&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=80%2C60&ssl=1 80w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=245%2C184&ssl=1 245w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/img_5857968375b93-600x450.png?resize=260%2C195&ssl=1 260w" sizes="(max-width: 600px) 100vw, 600px" data-recalc-dims="1" />

Every device is different, but 99% of them will have a key combination of a physical button to get you into the BIOS settings. Once you&#8217;re in, search for a way to change to boot-order or choose a device to boot. Don&#8217;t forget to save your settings. Again. every system is different but 99% of them supports changing this order. You can always Google the make and model of your device and find out how to change it. Take pictures before changing anything, so you can revert to the earlier state if things fail.

<img class="alignnone wp-image-754 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/boot-tab-bios-settings.jpg?resize=572%2C360&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/boot-tab-bios-settings.jpg?w=572&ssl=1 572w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/boot-tab-bios-settings.jpg?resize=300%2C189&ssl=1 300w" sizes="(max-width: 572px) 100vw, 572px" data-recalc-dims="1" />

This picture comes of a PC. Mac&#8217;s do also have a form of BIOS but it takes more tricks to get them to boot foreign storage devices.

## Making a bootable USB drive

Boot your computer like normal, and you&#8217;ll see that, as long as there is not other device with a bootable signature plugged in, changing the boot order will make no difference.

Now, there are many ways to make a bootable USB drive (or any storage medium), but the most user-friendly one is using a tool like [UNetbootin](https://unetbootin.github.io/). Click on the link and download it to your pc. It does not need admin rights so you can get it working on Windows, MacOS and Linux.

Now, plug in a spare USB drive, and remember its name. Start UNetbootin and follow a couple of easy steps (which you can also find on the homepage). For this article we are going to use a pre-configured setting, built in in UNetbootin. You can supply your own .ISO file (a DVD disk file, preprogrammed to boot and possible install the operating system on another hard drive) but that is not what we are going to do in this article.

**Select [Ubuntu](https://www.ubuntu.com/) and select its latest version**. Ubuntu is a linux-distribution which is made as user-friendly as possible. And it makes for an easy demonstration.

<img class="alignnone wp-image-755 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/unetbootin-windows7.png?resize=549%2C399&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/unetbootin-windows7.png?w=549&ssl=1 549w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/unetbootin-windows7.png?resize=300%2C218&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/unetbootin-windows7.png?resize=198%2C145&ssl=1 198w" sizes="(max-width: 549px) 100vw, 549px" data-recalc-dims="1" />

Select **type: USB Drive** and **select the right drive!** Please doublecheck this. So you don&#8217;t overwrite some important files. **YOU WILL LOSE ALL DATA ON THE USB DRIVE!**

When you checked your settings 3 times, **hit OK.**

The program will tell you when it&#8217;s done and you can eject your USB drive.

## Booting the USB drive

Now plug in you USB drive, and reboot you pc. If you BIOS boot order is setup correctly and your drive is bootable. You&#8217;ll get a screen like this:

<img class="alignnone wp-image-757 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/USB-Boot-Screen.jpg?resize=640%2C356&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/USB-Boot-Screen.jpg?w=720&ssl=1 720w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/USB-Boot-Screen.jpg?resize=300%2C167&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/USB-Boot-Screen.jpg?resize=640%2C356&ssl=1 640w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

Wait for the timer to time out or select &#8220;Try Ubuntu without installing&#8221;. Your Ubuntu installation will boot now! Do not install it, because you don&#8217;t want to overwrite your existing operating system. (you can even disconnect the cable to the other drive if you want).
  
Now you can try out a new operating system! In this &#8220;live&#8221; version of you operating system, everything that you do will be undone when you reboot your computer. You can not write to the USB drive. Have fun!

_REMINDER: Reversing this process is done by formatting the drive. This is different in every operating system, as always, for your specific system, just one Google search away._