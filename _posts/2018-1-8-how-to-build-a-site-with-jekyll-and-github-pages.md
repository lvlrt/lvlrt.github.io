---
title: 'How to build a site with Jekyll and Github Pages'
layout: post
categories:
  - webdev
tags:
  - terminal
  - open-source
  - decentralization
  - git
  - jekyll
---
![Jekyll and Pages Logo]({{ "/assets/jekyll_pages.jpg" | relative_url }})

_As a terminal-guy, I never really like the way Content Management Systems for websites work. Sure, they provide great UX (mostly) for the average user. But what if I want to skip all that and just make a barebones site that is easily manageable with the applications I already have on my system now?_

Welcome in the realm of static site generators. _Awesome... So if I want to change something I have to write HTML and CSS myself?_ No... That is a possibility, if you want a static page for your company or just a landing page but I wanted a blog so using something like [Jekyll](https://jekyllrb.com/) in combination with [Github Pages](https://pages.github.com/) is more logical. Let's walk through how to set it up.

## How it works
[Github Pages](https://pages.github.com/) supports [Jekyll](https://jekyllrb.com/), a static site generator. Which means, that there is no backend, database or hosting to configure. To add content to the site, you can use a markdown language, which makes it easy to write beautiful articles without messing with HTML and CSS.

You can have a [Github Pages](https://pages.github.com/) site for every repo (Private or Public) and also one extra per user. Without adding your own custom domain, your website URL will be:`https://<user>.github.io/<repo>`. You do have to enable this feature on the Github-platform itself before they get hosted.

## Creating the repo
To define the contents of your personal page, you have to make a repo with a name that exactly matches the following format:`<user>.github.io`. So in my case this is: `larsveelaert.github.io`. Normal project pages can have any name.

Now when you go to your new or existing repo and hit _Settings_, scroll down and you will find the Github Pages-section. Under _Source_, Choose your branch to host your files and that is all you will have to do for the hosting of your site. Easy right? 

## Search a theme
Now get your Google-skills on and search for "_Jekyll themes_". Often you will find Github-repo's with a demo link. If you like one, continue...

The easiest way to copy that theme is to clone the repo of that theme, and copy all its contents to your own repo. For example with the _[Whiteglass](https://github.com/yous/whiteglass)_-theme do:
```
git clone https://github.com/yous/whiteglass.git
cp -R whiteglass/* larsveelaert.github.io/

```
You will have to set 2 settings to the right value before the site will work, namely _baseurl_ and _url_. Your settings can be found in `_config.yml':
```
baseurl: "" # the subpath of your site, e.g. /blog
url: "https://larsveelaert.github.io" # the base hostname & protocol for your site, e.g. http://example.com
```

Now push your changes to your site's repo and you have succesfully copied the theme: 
```
git add -all
git commit -a -m 'theme setup'
git push
```

After a brief waiting period, browse to your website and you should see your chosen theme presented.

## Making changes and adding content
The great thing about this approach is that we can run [Jekyll](https://jekyllrb.com/) ourselves locally, so that we do not have to rely on one centralized way of changing content. 
Make sure _[Ruby](https://www.ruby-lang.org/en/)_ is installed and run the following commands in your repo:
```
gem install jekyll bundler
bundle exec jekyll serve
```
Now your site will be served on `localhost:4000`. If your want the make it rebuild the site if any of the files change, add the following option `--watch`. This is a great option to use when writing and previewing an article.

The main settings like page-title and social links will be set in the `_config.yml` file of your repo. Every theme is a bit different. But go through the docs of your specific theme and you will find lean ways how to change the navigation or how to add extra pages.

You can find your posts in the `_posts`-folder and it is there that you can just create a new file and write your articles in [Markdown](https://nl.wikipedia.org/wiki/Markdown). A great resource to learn the basics of Markdown is [this Github-page](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

## TIP: --drafts
Something that is really usefull is making a `_drafts` folder. Here you can add posts that will not be added to your regular feed. To process them and preview the result you can add the `--drafts` switch to your local `jekyll`-command and they will appear as most recent blog-posts.

## TIP: --incremental
When the --watch parameter is active, the site will be rebuilt whenever a file changes. If you only want to built the files that changed, use `--incremental`.

## Conclusion 
Using just a text-editor and the great infrastructure of Github Pages, makes it really easy to push changes with all the advantages we know from _[Git](https://git-scm.com/)_.
The option to edit offline, have offline-backups of the complete source and really manage the changes is a great option. Not having a backend to maintain is also a great win for security.

Have fun! 
