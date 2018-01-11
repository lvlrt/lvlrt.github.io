---
title: 'ARP-A-HOST, a script to resolve local network hostnames without extra services'
layout: post
categories:
  - Networking
tags:
  - decentralization
  - dns
  - ip
  - networking
  - terminal
---
I was in desperate need to find a way to not always have to scan the network or go over to another device to know its IP so I could ssh into it or use a service it was serving. This is what I found.

The internet is just a lot of devices, which can be addressed with an [IP address](https://en.wikipedia.org/wiki/IP_address). Those are unique in every network. To make the network more usable we use [domain names](https://en.wikipedia.org/wiki/Domain_name) with logical, easy to remember names like www.google.com or www.facebook.com.

This works by contactin a server with a fixed IP which provides [DNS](https://nl.wikipedia.org/wiki/Domain_Name_System) that resolves your domain name in an IP that your browser can go to. DNS-servers for the global internet are managed by governments and maintained by big companies like Google. This is why, If you want a domain name, it comes with a fee&#8230;

On local networks, its overkill (and quite some setup) to have a [DNS-server](https://www.lifewire.com/what-is-a-dns-server-2625854) to resolve the IP of devices but other programs like [zeroconf](https://nl.wikipedia.org/wiki/Zeroconf) and [mdns](https://en.wikipedia.org/wiki/Multicast_DNS) tried to have a more low-maintenance take on this problem. Short story: Still not ideal. You still have to have services running on all devices to ensure that your device gets registered&#8230;

## The solution

The ideal solution consists of:
  
&#8211; No changes needed to the network (so it works everywhere)
  
&#8211; Reliable
  
&#8211; Very little setup on the device itself and no services need to be running
  
&#8211; Cross-platform

The idea is to maintain a list of [MAC-addresses](https://en.wikipedia.org/wiki/MAC_address) of the devices I use (and sync those with [Dropbox](https://www.dropbox.com/) or [Git](https://git-scm.com/)), scan the network for these [MAC-addresses](https://en.wikipedia.org/wiki/MAC_address) and if found, add them to the /etc/hosts file. This is reliable because MAC-addresses are hard-coded in your hardware. You only need to know the MAC of your other devices and you can identify it. Every device has to have one. Adding to the /etc/hosts file is something that is supported by the core of linux so it is quite cross-platform on any device you can get linux working on.

So whenever my network-setup changes, or it has been a while and adresses may have switched, I run the following command;

<pre>bash ./ARP-A-HOST.sh ./HOSTS_MACS</pre>

**ARP-A-HOST.sh script:**
<pre>#DEPENDENCIES: arp-scan
#take $1 (first argument of the script) -&gt; file with mac adresses + hostname

tmpfile=$(mktemp /tmp/DYNAMIC-RESOLVE.XXXXXX) #tmp file for temporary results

#clear lines form the previous run in /etc/hosts file
sed -i.bak '/DYNAMIC_RESOLVE/d' /etc/hosts

#use the tool arp-scan to find all the devices on the network
if ! [ -z $2 ]; then
 arp-scan -l --localnet --interface=$2 &gt;&gt; $tmpfile
else
 for line in $(ip link | cut -d " " -f 2); do
 interface=${line::-1}
 arp-scan -l --localnet --interface=$interface 2&gt;/dev/null &gt;&gt; $tmpfile
 done
 #TODO interfaceoption -&gt; default wlan
fi
echo Arpscan Finished... Filtering results...

#reading the results one by one
while read -r line
do
 MAC=$(echo $line | cut -d " " -f 1)
 NAME=$(echo $line | cut -d " " -f 2)
 #grep MAC from ARP cache
 IP=$(cat $tmpfile | grep $MAC | cut -d$'\t' -f 1)

if ! [ -z "${IP}" ]; then
 echo Found $NAME at $IP! Adding to /etc/hosts...
 echo "$IP $NAME $NAME #DYNAMIC_RESOLVE"&gt;&gt;/etc/hosts #If the MAC is found, add to the /etc/hosts file
 fi 
done &lt; "$1"
echo Done with discovering hosts</pre>

**HOSTS_MACS file:**

<pre>00:90:f5:d6:5b:05 c1
c0:ee:fb:59:fc:23 s1
10:02:b5:d6:08:8a o1
b8:27:eb:4e:0c:42 kh1</pre>

The second file is just a list of MAC-addresses with a name you chose behind it. You can find your MAC-address in various ways, but the easiest is just running the ifconfig command on linux. (look next to &#8220;ether&#8221;)

```
[root@localhost]# ifconfig
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.13  netmask 255.255.255.0  broadcast 192.168.1.255
        ether 10:02:b5:d6:08:8a  txqueuelen 1000  (Ethernet)
        RX packets 221361  bytes 303819415 (289.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 112644  bytes 15169217 (14.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
...
```

You will need the tool arp-scan to run this script, you can find it in nearly all repositories. I run this script on an android phone, a desktop, chromebook and Raspberry Pi. So no worries.

If you check the /etc/hosts file you can see the following has changed:

<pre>#
# /etc/hosts: static lookup table for host names
#

#&lt;ip-address&gt; &lt;hostname.domain.org&gt; &lt;hostname&gt;
127.0.0.1 localhost.localdomain localhost
::1 localhost.localdomain localhost

# End of file
192.168.1.3 s1 s1 #DYNAMIC_RESOLVE
192.168.1.2 kh1 kh1 #DYNAMIC_RESOLVE</pre>

You can now use the hostnames in most commands and have them automatically resolved;

<pre>ssh s1 #resolves to the IP adress of my phone
ssh root@kh1 -p 22 #same but with more arguments</pre>

You can alias the _ARP-A-HOST.sh_-script command in you bashrc](https://demgeeks.com/qt-make-the-command-line-easier-with-aliases-and-functions/) to make it easier to use. That&#8217;s it! have fun!
