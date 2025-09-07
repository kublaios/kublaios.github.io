---
id: 34
title: 'ScriptCam integration in ASP.NET'
date: '2012-07-03T20:26:00+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=34'
permalink: /scriptcam-integration-for-net-scriptcamfordotnet-repository/
categories:
    - 'Blog Posts'
tags:
    - ASP.NET
    - JavaScript
---

Hi all,

Recently I had to take pictures from web camera in my Asp.Net page. There’s a great flash-based plugin works with jQuery, named [ScriptCam](http://www.scriptcam.com/). So I been dealing integration of this to my Asp.Net page.

You can see my bio above, I hate (unnecessary) postbacks. So it all works on client, faster, then I store the binary data of captured image in my Session variable. Next page I show it on Page\_Load event.

You can see the working example **[here](https://github.com/kublaios/ScriptCamForDotNet)**,  
Or you can download the project from **[here](https://github.com/kublaios/ScriptCamForDotNet)**.

That’s my first repository, I hope it becomes helpful for some fellas.
