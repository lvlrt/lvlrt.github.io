---
title: 'Quick tip: Running a terminal command through a proxy (Linux)'
layout: post
categories:
  - Quick Tips
tags:
  - linux
  - networking
  - proxy
  - terminal
  - tor
---
Sometimes, running a full [VPN](https://en.wikipedia.org/wiki/Virtual_private_network) is not necessary, or there is not enough bandwidth for all your traffic. 
In cases of using [Tor](https://www.torproject.org/) for example, tunneling all your traffic can even be dangerous!
So there is a way to specify per command or program if you want to have it tunnel its web traffic through the proxy or not.
This tool is called `proxychains`.

If you, for example want to download a file through a proxy with the `wget` command. Just prepend the command with `proxychains` and done!
```
$ proxychains wget www.remoteserver.com/fileIneed
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/libproxychains4.so
[proxychains] DLL init: proxychains-ng 4.12
--2018-01-08 23:18:33--  http://www.remoteserver.com/fileineed
```
Great! that works, but gives some output, you can silence the extra output with the -q flag.
```
proxychains -q ...
```
To set it up on your system follow the following steps:
### 1. Installation
For example on Arch Linux do:
```
pacman -Sy proxychains
```
On Ubuntu or other Debian-based distro's:
```
apt-get install proxychains
```
### 2. Configuration
Proxychains has a lot of configuration options but all you need to do, is go to the end of the file /etc/proxychains.conf and edit the last line;
```
#nano /etc/proxychains.conf
socks4  127.0.0.1 9050
```
It's preconfigured to use tor, 
That means a socks4 proxy on localhost port 9050.
  
Configure this to your needs, for example to use a SOCKS5 proxy made by SSH do this;
- Command to run to make the SSH connection
```
ssh remoteserver -D 5000
```
- Edit the configuration file like this:
```
socks5 127.0.0.1 5000
```
### 3. Done!

That's it, proxies can be amazing to change your appearance to the public internet, get to otherwise inaccessible content or tunnel your way out of a restrictive firewall/filter. So knowing how to use them in a terminal enviroment is essential.
