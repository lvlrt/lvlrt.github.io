---
title: 'Quick Tip: Keep a git repository small and lean'
layout: post
categories:
  - Quick Tips
tags:
  - development
  - git
  - optimalization
  - productivity
  - terminal
---
Git is an awesome tool. you can track your changes to a coding project, share your progress with others and let them suggest edits, fully collaborate on a project or make your own sync and backup system for you todo file.

A lot of people credit git to be one of the foremost reasons Linux has grown so much in recent years.

The only downside of a versioning system that keeps several branches in check can be size.
  
One of the best ways I found to clean up this history is the following;

  1. Go into your repo 
```
cd awesome-git-repo
```

  2. And run the following commands; 
```
git gc #Cleans up unnecessary object
git gc --aggressive --prune=now #Same, but deletes all files whom are not in the most recent branch tips
```

That it! The minimum healthy size of the .git folder in your repository is about the size of the files in the folder itself, so remember that! If this is still an enormous difference, try to find binaries or images in your folder. Small changes to those files make a lot of changes to the files and git will not know the difference. Git is made for source files only. So delete them, and run the command of step 2 again. Or configure your .gitignore file to exclude them from your git repository.
