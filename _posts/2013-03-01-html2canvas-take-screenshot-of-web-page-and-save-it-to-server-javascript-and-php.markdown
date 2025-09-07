---
id: 40
title: 'Taking screenshot of web page using html2canvas'
date: '2013-03-01T20:34:00+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=40'
permalink: /html2canvas-take-screenshot-of-web-page-and-save-it-to-server-javascript-and-php/
categories:
    - 'Blog Posts'
tags:
    - JavaScript
    - PHP
---

**FeedBack** is important. Usually, end-users struggle to clarify their problems. And you might be unreachable for a phone call or remote connection.

That causes a huge need of visualization. First solution that appears in mind is to capture the current screen of user.

However, when I tried to implement that, it wasn’t so easy as I expected. Some old ways offer ActiveX but it seems too outdated. Since there’s a bridge needed between client side and server, JS libraries are the best way.

There’s a great library, **[html2canvas](http://html2canvas.hertzen.com/)**. It is told to be reverse-engineered version of Google Plus’ way of taking screenshots.

When I first discovered this library, it took me a while to use for simplest implementation. I just wanted to visualize a div element. However, there was no single page to tell the whole path to follow, thus I had to combine various sources.

Here’s how you can easily use for taking a screenshot of a div:

**1- Import libraries**  
There are 3 libraries to import:

- jquery.js
- html2canvas.js
- jquery.plugin.html2canvas.js

You can download html2canvas and html2canvas jQuery plugin from [this link](https://www.kubilayerdogan.net/files/html2canvas_0.40_with_jquery_plugin.zip).  
Note: The source link contains html2canvas v0.40. I recommend you to check for a newer version and use it instead from [official html2canvas site](http://html2canvas.hertzen.com/).

I have used jquery.min.js v1.7.1 but you can try other versions. For this jQuery library, use [this link](http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js).  
Here’s first lines of code:

```
<script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
<script type="text/javascript" src="js/html2canvas.js"></script>
<script type="text/javascript" src="js/jquery.plugin.html2canvas.js"></script>
```

**2- Create your div**  
In my code, I used html2canvas for a div. You can use the whole body tag instead, it’s up to you.  
Attach a div element to the page with a certain id:

```
<div id="target">
<!-- Render your page inside of this div. -->
</div>
```

**3- Create a button and a hidden form**  
This part is important. In order to save the image to server, we need to pass captured image data with a form field.  
In 4th step, you’ll see JavaScript code that writes the image data to hidden field and posts the form.

```
<input type="submit" value="Take Screenshot Of Div" onclick="capture();" />
<form method="POST" enctype="multipart/form-data" action="save.php" id="myForm">
    <input type="hidden" name="img_val" id="img_val" value="" />
</form>
```

**4- JavaScript Code**

```
function capture() {
    $('#target').html2canvas({
        onrendered: function (canvas) {
            //Set hidden field's value to image data (base-64 string)
            $('#img_val').val(canvas.toDataURL("image/png"));
            //Submit the form manually
            document.getElementById("myForm").submit();
        }
    });
}
```

**5- Use the posted values**  
Here I used a form to post the value. You can use Ajax calls or whatever. I have a PHP file, save.php. In this file, we will both show the picture and save it to the server.

```
//save.php code
 
//Show the image
echo '<img src="'.$_POST['img_val'].'" />';
 
//Get the base-64 string from data
$filteredData=substr($_POST['img_val'], strpos($_POST['img_val'], ",")+1);
 
//Decode the string
$unencodedData=base64_decode($filteredData);
 
//Save the image
file_put_contents('img.png', $unencodedData);
```

**6- Enjoy your day**  
So that’s pretty much it. You can download the codes from **[Github repository page](https://github.com/kublaios/html2canvas-basic)**.

**7- Notes**

\* This tutorial uses html2canvas v0.4.0 – 30.1.2013. It has a new release, v0.4.1 – 7.9.2013, can be downloaded [from here](https://github.com/niklasvh/html2canvas/releases/tag/0.4.1). Though avaliability of newer versions haven’t been tested with the code above.

\* Some people have declared that they had issues with checkbox/radiobutton states on the captured image. Stu has shared his code to capture these elements with their states. I haven’t tried by myself but people who face problem about this may get some opinion from [his comment](https://www.kubilayerdogan.net/html2canvas-take-screenshot-of-web-page-and-save-it-to-server-javascript-and-php/#comment-6303).
