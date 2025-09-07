---
id: 45
title: 'DateTime ticks with Objective-C'
date: '2014-02-14T20:41:10+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=45'
permalink: /datetime-ticks-with-objective-c/
categories:
    - 'Blog Posts'
tags:
    - iOS
    - Objective-C
---

If you need to generate unique strings for every case, the concept of **DateTime.Ticks** is probably the best solution for you. It represents the count of seconds since the oldest **DateTime** object available.

However, **DateTime.Ticks** usage comes from **.NET** platform and it is that easy *in* **.NET**, *not in* **Objective-C**.

To generate a ticks number in Objective-C, you need to do the following:

1. Create an NSDate (01.01.0001), I know, rocking.
2. Calculate the ticks between this oldest date and \[NSDate date\] (which means now).
3. Turn that double variable to an integer. Well, long integer. Or… long long integer. (True story).

Here’s how I did it:

```
// Create the date formatter
NSDateFormatter *dateFormat = [[NSDateFormatter alloc] init];
[dateFormat setDateFormat:@"yyyyMMdd"];
 
// Create the oldest day possible
NSDate *ticksDate = [dateFormat dateFromString:@"00010101"];
 
// Get ticks difference between now and 01.01.0001 in double   
double ticksDouble = [[NSDate date] timeIntervalSinceDate:ticksDate];
 
// Turn it to a long long int
long long int ticks = llround(ticksDouble);
 
// Test it
NSLog(@"%lli",ticks);
```
