---
id: 66
title: 'PHP coding standards (pear.php.net)'
date: '2014-11-10T21:11:44+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=66'
permalink: /php-coding-standards-pear-php-net/
categories:
    - 'Blog Posts'
tags:
    - PHP
---

[pear.php.net/manual/en/standards.php](https://pear.php.net/manual/en/standards.php)

![PHP Coding Standards](/assets/img/posts/2014/php-coding/52543667.jpg)Always use to delimit PHP code, not the `<? ?>` shorthand.

```
<?php ?>
```

Control statements should have one space between the control keyword and opening parenthesis, to distinguish them from function calls.

```
if (true) {
   switch (condition) {
      case 1:
         action1;
         break;
      case 2:
         action2;
         break;
   }
}
```

You are strongly encouraged to always use curly braces even in situations where they are technically optional.

```
if (true) {
   // single line stuff
}
```

Split long if statements onto several lines keeping the question mark and the colon at the front.

```
if (($condition1 || $condition2)
    && $condition3 && $condition4
   ) {
   //code here
}
```

There should be one space on either side of an equals sign used to assign the return value of a function to a variable

```
$long_variable = foo($baz);
```

The CS require lines to have a maximum length of 80 chars. It is allowed to split parameters in function calls onto several lines.

```
$this->someObject->subObject->callThisFunctionWithALongName(
   $parameterOne, $parameterTwo, $aVeryLongParameterThree
);
```

The same applies not only for parameter variables, but also for nested function calls and for arrays.

```
someFunction(
   array(
      "foo" => "bar"
   )
);
```

To support readability, the equal signs may be aligned in block-related assignments:

```
$short     = foo($bar);
$thelonger = foo($baz);
```

![PHP Coding Standards](/assets/img/posts/2014/php-coding/52543649.jpg)Class and function declarations have their opening brace on a new line:

```
class Foo_Bar
{
}
function doThing()
{
}
```

Function declarations follow the “K&amp;R style” [http://en.wikipedia.org/wiki/Indent\_style#K.26R\_style](http://en.wikipedia.org/wiki/Indent_style#K.26R_style)

Arguments with default values go at the end of the argument list.

```
function doAnotherThing($id = 0, $doAgain = false)
{
}
```

`include_once` and `require_once` are statements, not functions. Parentheses should not surround the subject filename.

```
include_once "/file.php";
```
