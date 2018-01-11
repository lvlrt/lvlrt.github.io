---
title: "A transparent poor man's VPN with sshuttle"
layout: post
categories:
  - Networking
tags:
  - anonimity
  - encryption
  - linux
  - terminal
  - vpn
---
## Why?

There are a lot of reasons why you would want a [proxy](https://en.wikipedia.org/wiki/Proxy_server) or [VPN](https://en.wikipedia.org/wiki/Virtual_private_network), one of them is safety and protection from attackers at your location. For example if you are visiting sensitive things in your local coffee shop, other visitors could sniff your traffic because they are connected to the same network. From that point it is not only possible to read but also manupulate the ftraffic and possibly inject malicious code. Other usage cases are services who are only accessible behind a firewall or only if the user appears to be located at a certain specified IP address.

Most of the time, a proxy is one connection that gets rerouted while a VPN is a more client-friendly option to reroute all traffic from a system.
If you do not want to configure every application to use your proxy, and want to reroute all traffic, the tool `shuttle` is a perfect fit. 
It uses SSH under the hood so the permissions are managed through your existing SSH server so no extra user management is required.

I you are searching for a way to only route one command or application through a proxy have a look at [an earlier article](/2017/02/02/tip-running-a-terminal-command-through-a-proxy/) I wrote. 
In a lot of cases you don't need to search the applications native capability for configuring a proxy.
The more traffic you route through your ssh server, 
the more demanding and slow the operation becomes. So keep that in mind. Let's begin!

## How to install?

There are 2 things you will need;
  
- A ssh server on a remote device (there are tutorials specific to your flavour of linux)
- _[pip](https://pypi.python.org/pypi/pip)_, the package manager from python

To install the python package manager, run one of the following commands;
```
apt-get install python-pip # for debian and others
pacman -Sy python pip #for archlinux users
```

Now we can install _sshuttle_;
```
pip install sshuttle
```
Done!

### How to setup?

There are a lot of general options and specific routing options that can be configured with `sshuttle`.
But this article will just explain the transparent proxy (reroute all outgoing traffic). 
You can find the full documentation [here](https://sshuttle.readthedocs.io/en/stable/overview.html).

Now,for example, if we had an SSH server located at 192.168.13.2 and properly configured to accept connections from our address, we could run;

```
sshuttle --dns -vvr me@192.168.13.2:22 0/0
```
This will first make a connection to your ssh server and then start routing all traffic from your system to this location.
As you can see from the extra option. Also your DNS-requests will be tunneled.

To make sure there is not traffic leaked, look into network monitoring software like [wireshark](https://www.wireshark.org/). To test launch some applications and see if there is info being leaked.

### Tip for experts
You can make a bash function to automate the process and be able to quickly switch between servers.
For this example we will use the basic sshuttle command, but any one of them will work.
  
You can add a function to your .bashrc-file like this;
```
proxy () {
sshuttle --dns -vvr $1 0/0
}
```

The result of this will be that you can run one of the following in your bash-prompt:
```
$ proxy me@192.168.13.2:22 
$ proxy ssh_server1
```
And it will proxy all your traffic to a preconfigured ssh server.
To use your proxy as in the second example, you will have to configure your destination in the ~/.ssh/config file from your system to make it really useful
It will even take the keys that are loaded in your SSH-agent.
  
There is awesome documentation of this program, so [make sure to check it out](https://sshuttle.readthedocs.io/en/stable/overview.html)!
