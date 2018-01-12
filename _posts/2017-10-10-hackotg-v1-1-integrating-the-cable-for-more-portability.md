---
title: 'HackOTG (v1.1): Integrating the cable for more portability'
layout: post
categories:
  - HackOTG
tags:
  - badusb
  - embedded
  - OTG
  - rpi
  - usb
---
_This article is part of a series: [you can find the first article here](/2017/10/07/hackotg-v1-0-universal-portable-security-platform/)._

Before we start using our device, we have to make sure it is a good form-factor for our purpose and at this point, we will always need a USB-cable attached to the device, to be able to interface with it. In the future we will sometimes want to connect with WiFi so we can use the same connection to emulate different devices for our physical attacks.

No matter what, we will need the cable attached to it, be it for power, connectivity or attack. There are handy connections on the bottom of the Pi zero to make the following process easier. If you don&#8217;t want to perform this mod, you don&#8217;t need to, but it is very handy and makes the whole platform more compact and portable.

## What do you need?

  * Soldering iron, solder and tweezers (small wires)
  * Pi zero
  * Spare USB-cable
  * Small zip tie
  * Knife
  * Pliers
  * Glue

## Putting it all together

<img class="alignnone wp-image-871 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?resize=640%2C544&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?w=2585&ssl=1 2585w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?resize=300%2C255&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?resize=768%2C653&ssl=1 768w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?resize=1024%2C870&ssl=1 1024w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?resize=640%2C544&ssl=1 640w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?w=1280&ssl=1 1280w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-1821625988.jpg?w=1920&ssl=1 1920w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

No surprises here, snip of the USB-A side, strip the cable from the black plastic layer, and you&#8217;ll find a metallic shielding layer with 4 cables in it (white, green, red, black). Those are 2 data-cables and 2 for power. If you make 2 small indentations on both sides of the USB-port, you can tie a cable-tie around it and through the holes. Pull the cable-tie very tight (use pliers) and it wont move anymore.
  
Solder the cables to the pads with respective indicated color on the image below. The cables can be as short as 5cm. You can drop a bit of glue onto the wires to insulate them form each other again.

<img class="alignnone wp-image-867 size-full" src="https://i0.wp.com/demgeeks.com/wp-content/uploads/2017/10/Screenshot-2017-10-10-at-21.54.32.png?resize=640%2C307&#038;ssl=1" alt="" srcset="https://i0.wp.com/demgeeks.com/wp-content/uploads/2017/10/Screenshot-2017-10-10-at-21.54.32.png?w=695&ssl=1 695w, https://i0.wp.com/demgeeks.com/wp-content/uploads/2017/10/Screenshot-2017-10-10-at-21.54.32.png?resize=300%2C144&ssl=1 300w, https://i0.wp.com/demgeeks.com/wp-content/uploads/2017/10/Screenshot-2017-10-10-at-21.54.32.png?resize=640%2C307&ssl=1 640w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

## The result and what&#8217;s next?

Now you don&#8217;t have to carry a USB-cable together with the Pi, it is already integrated in the device! If you plug it straight into a power source (battery, wall-socket, &#8230;) it will boot. And if the device is also a USB-host, it will present itself as an Ethernet-to-USB adapter and we can SSH into it to perform our work. Now we can start using our HackOTG everywhere!

<img class="alignnone wp-image-864 size-full" src="https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=640%2C480&#038;ssl=1" alt="" srcset="https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?w=1765&ssl=1 1765w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=300%2C225&ssl=1 300w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=768%2C576&ssl=1 768w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=1024%2C768&ssl=1 1024w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=80%2C60&ssl=1 80w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=245%2C184&ssl=1 245w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=260%2C195&ssl=1 260w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?resize=640%2C480&ssl=1 640w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/10/wp-image-143906911.jpg?w=1280&ssl=1 1280w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

_You can find the next one here:_

<blockquote class="wp-embedded-content" data-secret="zjGS1XMwjZ">
  <p>
    <a href="/2017/10/11/hackotg-v1-2-basic-connectivity-to-internet/">HackOTG (v1.2): Basic connectivity to the internet</a>
  </p>
</blockquote>
