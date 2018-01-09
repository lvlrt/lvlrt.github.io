---
id: 889
title: 'HackOTG (v1.3): Creating our own hotspot on boot'
date: 2017-10-11T00:44:53+00:00
author: Lars Veelaert
layout: post
guid: https://demgeeks.com/?p=889
permalink: /hackotg-v1-3-creating-our-own-hotspot-on-boot/
tdc_dirty_content:
  - "1"
post_views_count:
  - "17"
image: /wp-content/uploads/2017/10/Screenshot-2017-10-11-at-16.05.17.png
categories:
  - Hacking
tags:
  - ap
  - command-line
  - dhclient
  - dhcp
  - dnsmasq
  - embedded system
  - ethernet
  - hackotg
  - hostapd
  - hotspot
  - ifconfig
  - ip
  - linux
  - networking
  - operating system
  - OTG
  - rpi
  - rpi zero
  - terminal
  - usb
  - wifi
  - wireless
---
_This article is part of a series: [you can find the first article here](https://demgeeks.com/hackotg-v1-0-universal-portable-security-platform/). If you missed the previous one, [it is here](https://demgeeks.com/hackotg-v1-2-basic-connectivity-to-internet/)._

## Checking the capabilities of our WiFi-interface

On any system you can run the following command to get the capabilities of the wireless interfaces attached to that device:

<pre>iw list</pre>

In the output of our Pi Zero W, we can see that is supports an AP mode, which means we can make the HostOTG create a hotspot on boot for controlling the device from a distance or without making use of the Ethernet-to-USB interface that the HostOTG emulates.

In the same output you can also see that we can combine modes, but with a couple restrictions:

<pre class="output">#{ managed } &lt;= 1, #{ AP } &lt;= 1, #{ P2P-client } &lt;= 1, #{ P2P-device } &lt;= 1,
 total &lt;= 4, #channels &lt;= 1</pre>

This means that we can set the interface in both AP and client mode. As a result you can have connectivity from an existing WiFi-hotspot and create also our own. Both hotspots must exist on the same channel, but that is no problem.
  
This is quite advanced but cool to keep in mind. In our case we want to set up an AP that starts on boot, so we can make our first connection. If you want to use both the AP and be a client to another, you&#8217;ll have to know and configure the environment before. This is not practical, so you&#8217;ll have to configure it over the emulated Ethernet-to-USB connection to make it work in every situation. You can find more on setting up combined modes [here](https://wiki.archlinux.org/index.php/software_access_point).

## Creating the hotspot

**Install the necessairy packages:**

<pre>sudo apt-get install hostapd dnsmasq</pre>

Now we must create a configuration file containing all the settings of the hotspot. I like to keep all configurations files in the home directory, so they are easily changed, copied and reused. We will be combining a lot of the programs in different setups, so it&#8217;s easier if they are easy to find.

**Create the file _hostapd.conf_:** (Change ssid and wpa_passphrase if you want to)

<pre>ssid=HackOTG
wpa_passphrase=raspberry
interface=wlan0
driver=nl80211
hw_mode=g
channel=6
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP</pre>

**Start the hotspot:**

<pre>killall wpa_supplicant dhcpcd hostapd dnsmasq #kill all unnecessary processes
sudo mount --bind /dev/urandom /dev/random #BUGFIX, to ensure good security
sudo hostapd hostapd.conf</pre>

## Run a DHCP-server on the hotspot-interface

An easy and lightweight option of looking for a DHCP-server is &#8220;dnsmasq&#8221;. We already installed the package so we go on and create the config file.

**dnsmasq.conf:**

<pre class="output"># disables dnsmasq reading any other files like /etc/resolv.conf for nameservers
no-resolv
# Interface to bind to
interface=wlan0
# Specify starting_range,end_range,lease_time
dhcp-range=10.0.0.3,10.0.0.20,12h
# dns addresses to send to the clients
server=8.8.8.8
server=8.8.4.4</pre>

**Start the DHCP-server and configure the interface:**

<pre>sudo dnsmasq -C dnsmaq.conf
sudo ifconfig wlan0 up 10.0.0.1 netmask 255.255.255.0</pre>

## Putting it all together

We can make scripts to start or stop the hotspot on-demand. Just create these scripts and run them to put the HackOTG in and out hotspot-mode.

**hotspot_start.sh:**

<pre>if [ $( mount | grep urandom | wc -l ) -eq 0 ]; then
 sudo mount --bind /dev/urandom /dev/random
fi
sh /home/pi/hotspot_stop.sh
sudo hostapd /home/pi/hostapd.conf&
sudo dnsmasq -C /home/pi/dnsmasq.conf&
sleep 2
sudo ifconfig wlan0 up 10.0.0.1 netmask 255.255.255.0</pre>

**hotspot_stop.sh:**

<pre>sudo killall dnsmasq hostapd dhcpcd wpa_supplicant
sudo ifconfig wlan0 0.0.0.0</pre>

_(optional) _You should add the hotspot_stop.sh script to the previous _connect\_wifi\_ssid.sh_ script from [this article](https://demgeeks.com/hackotg-v1-2-basic-connectivity-to-internet/). Otherwise you will not be able to connect to the internet anymore because the AP will be occupying the AP, add the _hotspot_stop.sh_ command to the script like this:

<pre>sh /home/pi/hotspot_stop.sh
sudo wpa_supplicant -B -i wlan0 -D wext -c ssid.conf
sudo dhcpcd --nohook wpa_supplicant wlan0</pre>

## Start the hotspot on boot

For all Debian-based distro&#8217;s there is a file _/etc/rc.local_ that runs all commands that are put in it when the device is fully booted. Simply add a line with the _hotspot_start.sh_ command to it (don&#8217;t forget the & at the end to make it a background-process). The command should go before the line containing _&#8220;exit 0&#8221;_.

**/etc/rc.local:**

<pre># By default this script does nothing.

# Print the IP address
_IP=$(hostname -I) || true
if [ "$_IP" ]; then
 printf "My IP address is %s\n" "$_IP"
fi

# put your scripts to boot here
sh /home/pi/hotspot_start.sh&

exit 0</pre>

Now you can restart the device and if everything is OK, it will create the WiFi-hotspot named _&#8220;HackOTG&#8221;_ with the password _&#8220;raspberry&#8221;_. If you waited for a minute and you can&#8217;t pick up the signal, you can still log in to your device over the emulated Ethernet-to-USB device. Now you have 2 ways to connect to your HackOTG!.

In [the next article](https://demgeeks.com/hackotg-v1-4-see-all-traffic-on-a-network/) we will further explore the possibilities to see and controll trafiic on a network.

<blockquote class="wp-embedded-content" data-secret="UxtFeiWmpQ">
  <p>
    <a href="https://demgeeks.com/hackotg-v1-4-see-all-traffic-on-a-network/">HackOTG (v1.4): See all traffic on a network with Promiscuous mode and Bettercap</a>
  </p>
</blockquote>