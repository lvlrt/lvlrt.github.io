---
title: 'Quick Tip: A transparent poor man's VPN with sshuttle (Linux)'
layout: post
categories:
  - Quick Tips
tags:
  - anonimity
  - encryption
  - linux
  - proxy
  - security
  - terminal
  - tunneling
  - vpn
---
## Why?

There are a lot of reasons why you would want a proxy, one of them is safety and protection from attackers at your location. For example if you are searching sensitive things in your local coffee shop, other visitors could sniff your traffic and in the worst case grab some passwords.

Other usage cases are services who are only accessible behind a firewall or only from a local IP address.

The tool `shuttle` is a perfect fit if you want to route ALL traffic from your current system to your external server. The permissions are managed through your existing SSH server so no extra user management is required.

I you are searching for a way to only route one command through a proxy have a look at [an earlier article](https://www.demgeeks.com/tip-running-a-terminal-command-through-a-proxy/) we wrote. The more traffic your will route through your ssh server, the more demanding the operation becomes&#8230; So keep that in mind. Let&#8217;s begin!

## How to install?

There are 2 things you will need;
  
- A ssh server on a remote device (there are tutorials specific to your flavour of linux)
- pip, the package manager from python

To install the python package manager;

<pre>apt-get install python-pip # for debian and others
pacman -Sy python pip #for archlinux users</pre>

Now we can install sshuttle;

<pre>pip install sshuttle</pre>

Done!

### How to setup?

There are a lot of options and routing options, this tutorial will just explain the transparent proxy (reroute all outgoing traffic). But you can find the documentation [here](https://sshuttle.readthedocs.io/en/stable/overview.html).

Now if we had a server at the ip address of 192.168.13.2 and properly configured to accept connections from our address, we could run;

<pre>sshuttle -vvr me@192.168.13.2:22 0/0</pre>

This will first make a connection to your ssh server and then start routing all traffic from your system to this location.
  
This software has a lot of options and this command will not route DNS, UDP or IPv6, only TCP. But in most cases, that is enough.

(If you are searching for complete safety from attackers on your network, you will probably have to launch a program like Wireshark and watch the packets that are not going through the proxy.)

### Tip for experts

We can use [an earlier quick tip](https://www.demgeeks.com/qt-make-the-command-line-easier-with-aliases-and-functions/) to have a command ready to quickly switch our &#8220;poor man&#8217;s VPN&#8221; to different locations.

For the example we will use the basic sshuttle command, but any one of them will work&#8230;
  
You can add a function to your .bashrc-file like this;

<pre>proxy () {
sshuttle -vvr $1 0/0
}</pre>

The result of this will be that you can run

<pre>$ proxy me@192.168.13.2:22 $ proxy ssh_server1</pre>

and it will proxy all your traffic to a preconfigured ssh server.
  
To use your proxy as in the second example, you will have to configure your destination in the ~/.ssh/config file from your system to make it really useful

There is awesome documentation of this program, so make sure to check it out!
  
https://sshuttle.readthedocs.io/en/stable/overview.html
