---
id: 59
title: 'Bulk renaming files using Sublime Text'
date: '2014-06-29T21:00:40+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=59'
permalink: /use-sublime-text-to-bulk-rename-your-files/
categories:
    - 'Blog Posts'
tags:
    - IDE
---

There are two cool plugins: One is dired, which allows you to use Sublime Text to manage your files and folders; and another is Text Pastry, which helps you to put increasing numbers to the multi-selected cursors (Of course, it is more than that, just not in this case).

Go ahead and combine this two. Yes, giving numbered names to contents of a folder. There were (in fact, probably still are) utilities existed that people buy for. Why pay, when you can do it with plugins?

First, install [dired](https://sublime.wbond.net/packages/dired) and [Text Pastry](https://sublime.wbond.net/packages/dired) plugins from [Sublime Text Package Control](https://sublime.wbond.net/packages/dired). No need to restart, just get into it directly by pressing `<strong>Cmd+Shift+P</strong>` -&gt; `<strong>dired: Goto Anywhere</strong>` -&gt; `<strong>Goto Directory</strong>` and then hit enter. It doesn’t matter what directory you are getting into since you can now navigate between folders thanks to dired.

Now, you can see the contents of the directory you are currently inside of, and you can see the shortcuts for navigation. Goto a directory that you want to bulk rename contents of and then press `<strong>R</strong>`. Now you can rename the files by using great abilities of Sublime Text such as locating matches, inserting multiple cursors and all.

![Bulk Rename Files using Sublime Text Dired and Text Pastry](/assets/img/posts/2014/sublime-text/bulk-rename-files-using-sublime-text-dired-and-text-pastry.png)

Assume that you have a set of images that were taken during one of your trips and you want to rename them like -&gt; trip\_1.jpg, trip\_2.jpg and so. You opened the folder in Sublime, selected all the images (lines), set all of the names to trip\_.jpg and placed your multi cursors after \_ (before .). Now what? Just hit `<strong>Cmd+Shift+P</strong>` again and then select `<strong>Text Pastry: From 1 To X</strong>`. That’s it. Now all of your files have numbers increased by 1 at the end of their names. Hit `<strong>Ctrl+Enter</strong>` to save changes and you’re good to go.

**Important Note:** Dired is no longer maintained so it will not appear on the package control. The developer was kind enough to share the latest working copy so I've uploaded it to Github. Install instructions can be found inside README.md. To download: [github.com/kublaios/dired](https://github.com/kublaios/dired)
