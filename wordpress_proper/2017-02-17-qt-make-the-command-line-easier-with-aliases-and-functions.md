---
title: 'Quick Tip: Make the command-line easier with aliases and functions (Linux)'
layout: post
categories:
  - Quick Tips
tags:
  - aliases
  - bash
  - linux
  - productivity
  - terminal
---
There are a couple ways to do this. You have aliases and functions. Aliases just make it possible to shorten commands or chain multiple commands together.
Functions extend this capability, by treating extra arguments as variables to be used in the function. Follow along!

## Aliases
One of the best ways to shorten long command is to use aliases. For example:
```
$ alias say_hello='echo hello'
$ say_hello
hello
$ say_hello how are you?
hello how are you?
```
It's that simple `say\_hello` will now be replaced with the command `echo hello`. Typing `say\_hello` in the bash shell for that session will make it print "hello". You can still append arguments to the end of the command, only the first command will be recognized and replaced. You can see this in the second example. So a more complex alias as;
```
$ alias folder='cd && mkdir '
folder new_folder #Goes to the homedir and creates a folder named new_folder.
```
So if you have very long commands, which start always the same, use this!

## Functions
There is also another way, to quickly make your own mini programs. This method is not as fast as an alias, but can be a lot more complex.
For example if you have a text file for your notes and you want a bash command to quickly add something to it, you can do;
```
$ note () { echo "$@" &gt;&gt; notes.txt ;} # define the function here
$ note First note
$ note Don't forget the first note
$ cat notes.txt #print the content of our notes file
First note
Don't forget the first note
```
In this case all arguments (that is what $@ means) after `note` will be added as a string to the notes.txt file. You can make the functions as complex as you want and really get some productivity out of this. I use this note example myself to quickly jot things down.

## Making the changes permanent
The aliases and functions defined are only working for that specific session. If you want those persistant you'll have to put them in a configuration file. If you are using the bash-shell. bashrc-file in your home-directory is automatically executed when you get a bash shell. You can use this to you advantage and add some commands to it.

<pre>$ nano ~/.bashrc # open the file
alias say_hello='echo hello'
alias folder='cd && mkdir ' #specify your command to run ...
note () { echo "$@" &gt;&gt; notes.txt ;} 

$ source .bashrc #reload your shell
$ say_hello #it works! now every time you login, all your commands will be there
hello</pre>

That&#8217;s it!
  
TIP: If you use multiple systems, and have a syncing system in place, you can put all your commands in a text file and add this to each .bashrc file:

<pre>source ~/&lt;syncdir&gt;/&lt;textfile_with_commands&gt;</pre>

That way, systems can have different and specific configurations while sharing a couple commands you have specified in the file.

Good luck!
