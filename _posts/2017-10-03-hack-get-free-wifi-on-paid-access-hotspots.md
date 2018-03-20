---
title: 'Hack: Get free WiFi on paid-access hotspots with a DNS tunnel'
layout: post
categories:
  - Hacks
tags:
  - dns
  - networking
  - proxy
  - security
  - tunneling
  - vpn
---
![header]({{ "/assets/dns-tunnel-monitoring.png" | relative_url }})

Everybody knows that you can&#8217;t connect to a WiFi-hotspot if it is secured and you don&#8217;t have a the password. But at airports, train-stations or homes with a routers from a big provider you will have a unsecured wifi hotspot, but when you connect to it and you open your browser, you will get prompted to log in or supply a credit card, etc&#8230; . Great if you have a login but otherwise you are stuck behind this [&#8216;captive&#8217; portal](https://en.wikipedia.org/wiki/Captive_portal) (that is what this page is called).

## How to bypass a captive portal

Often you can bypass a captive portal page by using DNS tunneling. You can test if this attack is possible by trying to ping google.com. Try this command;

<pre>ping www.google.com</pre>

<pre class="output">[root@localhost ~]# ping www.google.com
PING www.google.com (172.217.17.36) 56(84) bytes of data.</pre>

You don&#8217;t need to get a PING back, but you can see that www.google.com resolved to 172.217.17.36, this means that DNS is still working&#8230; strange if you don&#8217;t have an internet connection right?
  
So this means that [DNS](https://nl.wikipedia.org/wiki/Domain_Name_System) is still working. If we give the router a domain, it will resolve it by sending it to a nameserver and it will keep searching till if finds the IP, which it will send back to us. Now if you specify a [subdomain](https://en.wikipedia.org/wiki/Subdomain) (&#8220;mail&#8221; in mail.google.com) it will ask the nameserver of the subdomain (if it exists) to give the right IP and relay it back.

This means that if we control the subdomain we are looking up, and we control the nameserver assosiated with it, we can decide which IP (or [better DNS record](https://en.wikipedia.org/wiki/List_of_DNS_record_types)) to send back. The result is that we can upload data through an extra attached subdomain and download data encoded in the DNS-record that is send back. This process is called DNS-tunneling. To encode this data, there are multiple tools available but [iodine](http://code.kryo.se/iodine/) is a great one, and this is that is used in this article.

## What you need to setup:

  * Spare (Linux) machine at home, this can be an existing server or desktop.
  * [Dynamic DNS](https://en.wikipedia.org/wiki/Dynamic_DNS) that resolves to public IP of server (explained in this article)
  * A subdomain that holds a NAMESERVER-record (explained in this article)
  * [Iodine](http://code.kryo.se/iodine/)-daemon on the server (explained in this article)
  * Router which you can setup with static IP&#8217;s and [Port forwarding](https://en.wikipedia.org/wiki/Port_forwarding)

## Setting up the DDNS and NS-record

You can use [freedns.afraid.org](https://freedns.afraid.org/) for the [dynamic DNS](https://en.wikipedia.org/wiki/Dynamic_DNS) and the [NS-record](https://en.wikipedia.org/wiki/List_of_DNS_record_types#NS). So make an account and go to &#8220;subdomains&#8221;. You need to make 2 subdomains. One is a normal A-record (domain name to IP) and one of the type NS that is redirected to the A-record so it points to the public IP of the server at home.

![setup ddns on site]({{ "/assets/iodine1.png" | absolute_url }})
  
For the A-record fill in a sub domain (can be anything, just remember it) and choose a domain (these are donated by a large community to use). fill in the captcha and done.
  
The NS-record do the same, but change the destination to the A-record you just made (<subdomain>.<domain>).

The IP of the A-record was auto-filled when the subdomain was created but it needs to be periodicly updated by the server, so it keeps pointing at the public IP of you home-router with the server behind it. There are many ways to do this ([can be found here](https://freedns.afraid.org/scripts/freedns.clients.php)), but one of the easiest is fetching an url with a curl-command every 60 seconds.If you go to [freedns.afraid.org/dynamic](https://freedns.afraid.org/dynamic/), you can choose your subdomain of your A-record and get the link behind &#8216;direct link&#8217;.

![extra setup]({{ "/assets/iodine2.png" | absolute_url }})

It looks like this:

<pre>https://freedns.afraid.org/dynamic/update.php?&lt;your-key&gt;</pre>

**ddns.sh:**

<pre class="output">while true
do
    curl https://freedns.afraid.org/dynamic/update.php?&lt;your-key&gt;
    sleep 60
done</pre>

Put this on the server at home. This is a script that calls the url every 60 seconds so it keeps updated over time. You probably want to execute it on boot. On a system with systemd you can use this service file:

**/usr/lib/systemd/system/ddns.service:**

<pre class="output">[Unit]
After=network.target
Requires=network.target

[Service]
ExecStart=/usr/bin/sh /root/ddns.sh

[Install]
WantedBy=multi-user.target</pre>

Then run these commands to check if it works and if so, make it persistent.

<pre>sudo systemctl start ddns.service</pre>

sudo systemctl status ddns.service #check if ok sudo systemctl enable ddns.service #make it persistant over boot

Now if you ping your A-record subdomain you will get the IP of your router at home. The ddns.service will keep it that way.

## Configuring the Iodine-daemon

First you have to install the iodine program on the server at home, find it in the repository as &#8220;iodine&#8221; or &#8220;iodine-server&#8221;. For Arch-Linux this is the following command:

<pre>pacman -Sy iodine</pre>

Now you want to configure the daemon with the info of your record and make it start at boot. This is the command that has to run on boot:

<pre>/usr/bin/iodined -f -c -l $IODINE_BIND_ADDRESS -n $IODINE_EXT_IP -p $IODINE_PORT -P $IODINE_PASSWORD -u $IODINE_USER $TUN_IP $TOP_DOMAIN</pre>

On Arch-Linux the service file is already supplied and the settings can be found at **/etc/conf.d/iodined**:** **(you only need TOP\_DOMAIN and IODINE\_PASSWORD)

<pre class="output"># Address and subnet to use for the tunnel (default mask is /27)
 TUN_IP="172.18.42.1/24"

# Password (32 characters max)
 IODINE_PASSWORD="mypassword"

# The domain you control, see documentation.
 TOP_DOMAIN="&lt;subdomain&gt;.&lt;domain&gt;.com"

# UDP port iodined should listen on.
 IODINE_PORT="53"

# Local IP address iodined should bind to.
 IODINE_BIND_ADDRESS="0.0.0.0"

# External IP of your iodined server, used in DNS answers.
 IODINE_EXT_IP=""

# The user iodined should run as.
 IODINE_USER="nobody"</pre>

When ready run these commands to make it run at boot on an Arch-Linux system:

<pre>sudo systemctl start iodined.service
sudo systemctl status iodined.service
sudo systemctl enable iodined.service</pre>

_Note: You really want to set a password, this is not for encryption, this is not encrypted in any way, but for authentication, so not everybody can use your DNS-tunnel. _

Now you need to port-forward port 53 (the port DNS uses) on your home-router to your device, used as server. This is different for every router but generally you also want to make the internal IP &#8220;static&#8221; for this device so it does not change after a reboot. Now your Iodine-daemon is accessible from outside of your network.

You can check if everything is properly setup by filling it in on this page <http://code.kryo.se/iodine/check-it/>. It will tell you were it failed if something is not in order. If it works, let&#8217;s bypass some captive portals!

## Usage

You should install the [iodine](http://code.kryo.se/iodine/)-client before you get stuck somewhere without internet.
  
It is found in many repositories as &#8220;iodine&#8221; or &#8220;iodine-client&#8221;. On Arch-Linux:

<pre>pacman -Sy iodine</pre>

Then to use this technique when you are behind a captive portal, make sure you are connected to the hotspot. Then, when you have confirmed there is a hole in the security of the captive portal with the ping method described in the first part of this article, run the following command. (it will ask a password if set)

<pre>iodine &lt;subdomain&gt;.&lt;domain&gt;</pre>

<pre class="output">Enter password:
Opened dns0 
Opened IPv4 UDP socket 
Sending DNS queries for ****.****.*** to 192.168.1.1
Autodetecting DNS query type (use -T to override).
Using DNS type NULL queries 
Version ok, both using protocol v 0x00000502. You are user #0 
Setting IP of dns0 to 172.18.42.2 
Setting MTU of dns0 to 1130 
Server tunnel IP is 172.18.42.1 
Testing raw UDP data to the server (skip with -r) 
Server is at 192.168.1.2, trying raw login: OK 
Sending raw traffic directly to 192.168.1.2
Connection setup complete, transmitting data. 
Detaching from terminal...</pre>

Awesome! We have a tunnel. Now we can contact every port on the server at 172.18.42.1, for example we can SSH to the server:

<pre>ssh user@172.18.42.1</pre>

Now to tunnel a internet connection through, you can use the built in SOCKS-proxy of SSH. To use it supply the -D option with a local port to put the proxy on. All your internet will be tunneled over the ssh-connection if you specify the proxy in your program. example of the command:

<pre>ssh user@172.18.42.1 -D 5000</pre>

Research how to setup your programs on your system to use the proxy or use this quick tip which explains how to [run commands through a proxy](https://larsveelaert.github.io/2017/02/02/running-a-terminal-command-through-a-proxy/). 
There is also a transparent way that tunnels all internet through a ssh tunnel [explained here](https://larsveelaert.github.io/2017/02/17/poor-man-vpn/).

Hope you learned something about how the internet works and have some free internet in the meanwhile. Have fun!
