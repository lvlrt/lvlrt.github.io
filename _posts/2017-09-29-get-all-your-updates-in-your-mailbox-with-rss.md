---
id: 733
title: Get all your updates in your mailbox with RSS
date: 2017-09-29T10:11:30+00:00
author: Lars Veelaert
layout: post
guid: https://demgeeks.com/?p=733
permalink: /get-all-your-updates-in-your-mailbox-with-rss/
tdc_dirty_content:
  - "1"
post_views_count:
  - "42"
image: /wp-content/uploads/2017/09/tmi.jpg
categories:
  - Productivity
tags:
  - bash
  - cli
  - command-line
  - console. self-hosted. self-hosting. DIY
  - decentralization
  - development
  - diy
  - email
  - linux
  - mutt
  - networking
  - news
  - productivity
  - rss
  - terminal
  - tips
  - updates
---
So I have a couple sites I check regularly&#8230; YouTube, a couple blogs, Reddit-pages, LinkedIn and some news-sites. Often it happens that I miss some interesting article, or in general, I have to remember to check it. It&#8217;s not very productive to have the feeling you have to check everything, all the time.

A good way to go about this is subscribing to newsletters and reviewing settings of your online accounts to have messages send to your mail inbox. Great, but not every simple site has this option and newsletters often are to bloated with ads and calls to action to be fun to read.

I&#8217;ve wanted to solve this problem for quite a while but never found a good solution. My general philosophy is having everything on every device working in exactly the same way. I make sure I get a terminal on every device and from there I open my Linux environment with all my tools setup and synced.

## And then I discovered RSS

Most sites today offer a RSS-feed. This is a file that resides somewhere on the domain (often linked somewhere on the homepage) and is a very simple XML file that has a summary of the meaningful changes to a site. Most engines like WordPress and Joomla have it by default. And any major site will support it (I havent found one that does not). Sometimes a Google-search is needed to find the right URL for sites like Reddit, but it&#8217;s (nearly) always there.

## The problem

So if you have all your links of RSS-feeds, you can put them in a program that handles all the news for you. In the early days there was [Google Reader](https://www.google.com/reader/about/) but that is gone right now (it did not fit the Google marketing anymore).There are apps like [Feedly](https://feedly.com/) to replace it and many [others](https://www.howtogeek.com/128487/the-best-free-rss-readers-for-keeping-up-with-your-favorite-websites/).

These are definitely great, but I don&#8217;t like standalone programs. I&#8217;d like my news to end up in my mailbox. I tried online RSS to Email converters like [Blogtrottr](https://blogtrottr.com/) but never really liked it. It failed often and I had to go online and log in to add extra feeds. I also like to use as few resources or services as possible.

Then local converters such as [newspipe](https://www.google.be/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiBm8yikMrWAhWBCBoKHfDdDi0QFggwMAE&url=http%3A%2F%2Fnewspipe.sourceforge.net%2F&usg=AFQjCNEANFszQVtDJDB4BQN6CgfnLXsc4w) and [rss2email](http://www.allthingsrss.com/rss2email/) got my attention but they are the main page was down and rss2email needed a specific format to read the links from and would write my mails into an incompatible and older version of mailbox.

## The DIY fix

So I realized [rss2email](http://www.allthingsrss.com/rss2email/) was written in Python (Yes!) and most of its heavy-lifting was done by a module called &#8220;feedparser&#8221;. So I wrote my own script to take a list of URL&#8217;s (of rss-feeds) and add them my local inbox in Maildir-format (I use the terminal application [mutt](https://nl.wikipedia.org/wiki/Mutt) for my email). Many email-programs use this standardized format so research if yours is compatible. If you want to read a great article on how to set up Mutt I recommend [this Arch Linux post](https://wiki.archlinux.org/index.php/mutt) and [The Homely Mutt](http://stevelosh.com/blog/2012/10/the-homely-mutt/).

<img class="alignnone wp-image-739 size-full" src="https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-09-28.png?resize=640%2C122&#038;ssl=1" alt="" srcset="https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-09-28.png?w=970&ssl=1 970w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-09-28.png?resize=300%2C57&ssl=1 300w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-09-28.png?resize=768%2C146&ssl=1 768w, https://i2.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-09-28.png?resize=640%2C122&ssl=1 640w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

So this is the command you can add to a macro or [alias](https://demgeeks.com/qt-make-the-command-line-easier-with-aliases-and-functions/) to fetch the feeds form the _rss.txt_ file in the _~/RSS/_ directory and parse them offline into emails which will be added to the _~/MAILDIR/account/INBOX_

<pre>python ~/rss2maildir.py ~/RSS ~/MAILDIR/account/INBOX</pre>

<img class="alignnone wp-image-742 size-full" src="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-12-37.png?resize=640%2C154&#038;ssl=1" alt="" srcset="https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-12-37.png?w=734&ssl=1 734w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-12-37.png?resize=300%2C72&ssl=1 300w, https://i1.wp.com/demgeeks.com/wp-content/uploads/2017/09/Screenshot_2017-09-29_12-12-37.png?resize=640%2C154&ssl=1 640w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" />

You can find the script and an example rss.txt in [this Github-repo](https://github.com/polarsbear/rss2maildir). Feel free to give tips and share your experiences!