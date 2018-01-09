---
id: 134
title: 'Quick Tip: GnuPG-based password manager (Linux)'
date: 2017-09-23T11:09:21+00:00
author: Lars Veelaert
layout: post
guid: http://www.demgeeks.com/?p=134
permalink: /quick-tip-gnupg-based-password-manager/
post_views_count:
  - "39"
tdc_dirty_content:
  - "1"
td_post_theme_settings:
  - 'a:1:{s:16:"td_post_template";s:15:"single_template";}'
image: /wp-content/uploads/2017/09/Custom_Programmable_Access_Control_Systems.jpg
categories:
  - Quick Tips
tags:
  - anonimity
  - bash
  - command-line
  - encryption
  - linux
  - productivity
  - security
  - terminal
  - tips
---
In this article we will setup a secure password manager. You probably use the same password over and over again on multiple sites/devices/applications. Not so great! If one service gets compromised, an attacker can wreak some serious havoc. **YOU SHOULD USE DIFFERENT PASSWORDS!**
  
But this is hard, so many store their passwords in a passwords.txt file on their desktop, or even better, in a service like Dropbox storing all passwords together and keeping that file synced. Copying passwords will also circumvent [keyloggers](https://en.wikipedia.org/wiki/Keystroke_logging) active on your system. So awesome right?!

Not really, If one of your devices gets compromised, or Dropbox makes a mistake, you are screwed. You should still have an extra layer of access protection between the file and the passwords, syncing with Dropbox can be fine, but you have to encrypt the file.

## About password managers

One option is using tools like [Keepass](http://keepass.info/) or [1Password](https://1password.com/). But you are depending on that service, its not modular (you can not choose the way it gets synced). I don&#8217;t like trusting these services. Not only the communication but things like encryption type, memory leaks, temporary files can all be badly designed. [Open-source](https://www.coredna.com/blogs/comparing-open-closed-source-software) tools are the way to go, because you can trust AND verify.

The best way to do this is with [asymmetric encryption](https://en.wikipedia.org/wiki/Public-key_cryptography). Their are standards such as [PGP](https://nl.wikipedia.org/wiki/Pretty_Good_Privacy) ([GnuPG](https://www.gnupg.org/) on linux) who make this easy. The encryption is very strong because you use a long encryption key, to decrypt your target file.
  
The long encryption key is stored locally on your machines, and you need a passphrase to use it. So even if the can get access to the long secure encryption key (private key). They will still need the passphrase.

This is not all that [GnuPG](https://www.gnupg.org/) can do, it is an amazing piece of work, if interested, you should check it out. A nice place to get started with using it is this [Arch Linux page](https://wiki.archlinux.org/index.php/GnuPG) about GnuPG.

I use the terminal everywhere and there is a cli-tool named [pass](https://wiki.archlinux.org/index.php/Pass) to automate the usage of GnuPG for passwords. We will use that to manage our passwords.

## Dependencies

You first have to install pass and GnuPG and more important, set up GnuPG and have it make a key for you (you can of course use an already existing key, but then you would not be reading this). Do what is right on your system:

**Debian-based;**

<pre>sudo apt-get install pass gnupg</pre>

**Arch Linux; **

<pre>sudo pacman -Sy pass gnupg</pre>

The cool thing about the pass password-manager, is its cross platform uniform behavior. You know how to use it on one platform and you know it on all your other devices (more on syncing the passwords later)

## Setup your encryption key

<pre>gpg --full-gen-key</pre>

Now gpg will ask you a couple questions, like what kind of key you want, press 1.
  
I chose a key-size of 4096, which is the highest possible. Then put in 0 for a everlasting key, confirm you choice. and STOP!<img class="aligncenter wp-image-141 size-full" src="https://i0.wp.com/www.demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.27.40.png?resize=640%2C507&#038;ssl=1" alt="setup gpg key" srcset="https://i0.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.27.40.png?w=657&ssl=1 657w, https://i0.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.27.40.png?resize=300%2C237&ssl=1 300w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

Now put a YouTube video playing, setup a file copy from and to your disk for 10 minutes or something. Then go back to your console and it will have asked for a password and fill in you name, email and a comment (like &#8216;created on 27/12/2016&#8217;). Confirm and you are done.
  
You can close your YouTube video and the file transfer. This is to generate some computer noise. Which will enprove the randomness of the generated key!

To test if the key was successfully created:

<pre>gpg --list-keys</pre>

<img class="aligncenter wp-image-142 size-full" src="https://i1.wp.com/www.demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.29.25.png?resize=640%2C148&#038;ssl=1" alt="List keys" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.29.25.png?w=832&ssl=1 832w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.29.25.png?resize=300%2C70&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.29.25.png?resize=768%2C178&ssl=1 768w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

Ok great!, let us now get pass setup, you can do that with this command: (where the <id> value is your email or name you just put in)

<pre>pass init &lt;id&gt;</pre>

<img class="aligncenter wp-image-143 size-full" src="https://i1.wp.com/www.demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.30.29.png?resize=640%2C58&#038;ssl=1" alt="Init password store" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.30.29.png?w=826&ssl=1 826w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.30.29.png?resize=300%2C27&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2016/12/Screenshot-2016-12-27-at-18.30.29.png?resize=768%2C70&ssl=1 768w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

Setup done!

## Usage

**To insert a password in your password-store:**

<pre>pass insert stuffathome/thingseverybodyshouldknow/wifipassword</pre>

You can keep adding or removing &#8216;/&#8217; signs to make more divisions in your password-store.
  
These will just be added as descending folders.

To have it instead generate a password for you (if you want to be extra safe) and store it in an encrypted file:

<pre>pass generate stuffathome/thingseverybodyshouldknow/wifipassword</pre>

**No password asked? Isn&#8217;t this unsafe?**
  
The way we encrypted this password-store is asymmetrical&#8230; so everybody can encrypt, but only the ones with a private key can decrypt.

**To list the available passwords just type:**

<pre>pass show</pre>

**To retrieve the password we just created, do:**

<pre>pass show stuffathome/thingseverybodyshouldknow/wifipassword</pre>

And now it will ask for your passphrase of the gpg key you specified in the setup command. Then it should echo the password out. And thats really all you need to know to use this password manager. The key will be available for a small time until it timed out. That&#8217;s it!

## Syncing your passwords

To synchronize your password manager between devices. You need to synchronize 2 sets of files. The first one is you gpg key. Those are stored in the folder ~/.gnupg by default. You can change the location where GnuPG checks for these files with setting the $GNUPGHOME variable:

<pre>export GNUPGHOME="~/keys"</pre>

Now you can move your .gnupg folder to a new location and put the new location in this variable. REMINDER: you have to put this command in your ~/.bashrc file or alias it with your pass command (more on that later).

You passwords are stored in a directory, called a store, this is the one that pass has setup for you. The default location is ~/.password-store. If you use change the variables $PASSWORD\_STORE\_DIR and $PASSWORDS\_STORE\_GIT to the location where your store is. So all this in one command (put this in ~/.bashrc):

<pre>alias pass='GNUPGHOME=~/keys PASSWORD_STORE_DIR=~/DATA/PASSWORDS PASSWORD_STORE_GIT=~/DATA/PASSWORDS pass'</pre>

This [alias](https://www.demgeeks.com/qt-make-the-command-line-easier-with-aliases-and-functions/) command, will make your pass command always use these variables. You copy the passwords-store and key to a service like Dropbox or in a synced git-repository, put the path to these directories in the command and you are set! Please keep the key and password-store seperate, it is still protected by a passphrase but you lose the protection of you 4096-bit key.

That&#8217;s it! Good Luck with your password manager!