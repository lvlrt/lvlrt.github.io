---
title: 'HackOTG (v1.2): Basic connectivity to the internet'
layout: post
categories:
  - HackOTG
tags:
  - embedded
  - networking
  - rpi
  - wifi
  - wireless
---
_This article is part of a series: [you can find the first article here](/2017/10/07/hackotg-v1-0-universal-portable-security-platform/). If you missed the previous one, [it is here](/2017/10/10/hackotg-v1-1-integrating-the-cable-for-more-portability/)._

After the previous article, we can plug-in our HackOTG and log in with SSH over the emulated Ethernet-adapter. Now, we don&#8217;t have internet-connectivity yet&#8230; Sometimes we will want to have an internet-connection to install new packages, backup our device or have a new way to connect to our device. In this article we will go through the basics of networking on your HackOTG.

## Setting up the interface and scan

Log in to your HackOTG over the emulated Ethernet device:

<pre>ifconfig usb0 192.168.7.3
ssh pi@192.168.7.2 #password is "raspberry"</pre>

**Activate your Wifi-interface:**

<pre>sudo ifconfig wlan0 up</pre>

**Scan for available Wifi-Hotspots:**

<pre>sudo iwlist wlan0 scan | less</pre>

## Associate with the access point

There are a couple different security mechanisms, in the previous performed scan you&#8217;ll find which one your chosen hotspot uses.

### WEP

This is one of the [oldest security mechanisms](https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy), which has many flaws but this is how you connect: (replace &#8220;testessid&#8221; and &#8220;safestpasswordever&#8221;)

<pre>sudo iwconfig wlan0 essid testessid key s:safestpasswordever</pre>

### WPA

A common security mechanism is [WPA](https://en.wikipedia.org/wiki/Wi-Fi_Protected_Access). WPA has many flavours but most of them will be accesible with the following steps.

**Create a config-file:** (replace &#8220;MYSSID&#8221; and &#8220;passphrase&#8221;)

<pre>wpa_passphrase <em>MYSSID passphrase</em> &gt; <em>MYSSID</em>.conf</pre>

This will create a config-file with a hashed passphrase (for security reasons). If it is a public (unsecured) WiFi, you can create the same file with these contents.

<pre>network={
 ssid="MYSSID"
 key_mgmt=NONE
 }</pre>

**To connect:** _(replace &#8220;interface&#8221; and &#8220;MYSSID&#8221;)_

<pre>sudo wpa_supplicant -B -i <i>interface</i> -D wext -c <em>MYSSID</em>.conf</pre>

The driver &#8220;wext&#8221; can be changed out for other drivers found in the &#8220;wpa_supplicant -h&#8221; command. With the built-in wifi on the Pi zero, you can use &#8220;wext&#8221;. The &#8220;&&#8221; at the end of the command will run it in the background.

## Requesting an IP

Now that we are assosiated and authenticated, we need an IP, We can get it from the DHCP server running on the router with this command:

<pre>sudo dhcpcd --nohook wpa_supplicant wlan0</pre>

## Automate the process

You can write a bash script, so you can connect to a wifi-hotspot with one command. You can make multiple scripts and config-files to connect to different hotspots.

<pre>sh connect_wifi_ssid.sh</pre>

**connect\_wifi\_ssid.sh:**

<pre>sudo wpa_supplicant -B -i wlan0 -D wext -c ssid.conf
sudo dhcpcd --nohook wpa_supplicant wlan0</pre>

It is a good idea to make a **connect\_wifi\_HELP.txt,** that contains the wep and wpa_passphrase command and some guidelines to connect to WEP and WAP (so you don&#8217;t need this article).

**connect\_wifi\_HELP.sh:**

<pre>##SCANNING
sudo iwlist wlan0 scan
##WEP
sudo iwconfig wlan0 essid testessid key s:safestpasswordever
##WPA 
wpa_passphrase <em>MYSSID passphrase</em> &gt; <em>MYSSID</em>.conf 
sudo wpa_supplicant -B -i wlan0 -D wext -c MYSSID.conf
sudo dhcpcd --nohook wpa_supplicant wlan0
#unsecured? change the conf file to: 
network={ 
 ssid="MYSSID"
 key_mgmt=NONE 
}</pre>

## Forwarding the connection

You can forward the connectivity of your HackOTG through your emulated Ethernet device by simply enabling ipv4-forwarding with this command:

<pre>sudo sh -c "echo 1 &gt; /proc/sys/net/ipv4/ip_forward"</pre>

This can be handy if you need to register or fill in a form in a captive portal. Or if you just need a WiFi-connection.

## Checking the internet connection

You will have 2 active interfaces now. By performing the _&#8220;route -n&#8221;_ command, you can see the way your traffic is going to be handled. Sometimes you will want to choose the connection to use for you internet connectivity. You can do that with these scripts:

**route_wlan0.sh:** (to direct all traffic through the WiFi-connection)

<pre>sudo route del default
sudo dhcpcd -n wlan0</pre>

**route_usb0.sh:** (to direct all traffic the emulated ethernet connection)

<pre>sudo route del default
sudo dhcpcd -n usb0</pre>

Remember, if you dont&#8217;t have connectivity to the internet, check if the proper interface is on the first line in the output of the _&#8220;route -n&#8221;_ command. But now that we have connectivity, we can update all our software with the following commands:

<pre>sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade</pre>

Great, we can connect to wifi-hotspots now, which we can not only use for an internet connection but if you know the IP (use &#8216;ifconfig&#8217;) you can connect to it over wifi.
  
Next, we will setup our own WiFi-hotspot, so we don&#8217;t have to configure a WiFi-hotspot over the emulated Ethernet-device if we want to SSH to the device over WiFi.

_You can find the next one here:_

<blockquote class="wp-embedded-content" data-secret="tVHiU01lqM">
  <p>
    <a href="/2017/10/11/hackotg-v1-3-creating-our-own-hotspot-on-boot/">HackOTG (v1.3): Creating our own hotspot on boot</a>
  </p>
</blockquote>
