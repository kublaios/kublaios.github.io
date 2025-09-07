---
id: 36
title: 'Injecting PHP DateTime to SQL'
date: '2012-12-23T20:28:08+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=36'
permalink: /generate-datetime-value-in-php-for-using-in-mysql-command/
categories:
    - 'Blog Posts'
tags:
    - PHP
    - SQL
---

When we work with date and/or time values in MySQL, it’s always easy to use [built-in MySQL functions](http://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html) such as NOW(), DATE\_ADD(), TIMESTAMP() etc… Now imagine you have to pass date/time values to MySQL from PHP variables. First time I tried that, it didn’t work out so easy for me.

Yes, if you simply take one column value and use it again, you have no problem. But here’s an example that you take date and time from MySQL column, **add 1 seconds** to that value and use it in query again:

```
//Here's just stuff in order to get a datetime value from MySQL DB.
$query = "SELECT TOP 1 `date` FROM `table`";
$result = mysql_query($query, $db_link);
$date_time_val = "";
if ($result) {
    while($row = mysql_fetch_assoc($result)) {
        //Here we get the value.
        $date_time_val = $row["date"];
    }
}
 
//Now we add one second to this value and use it again.
//First, use it for generating a DateTime variable from PHP library.
$time = new DateTime($date_time_val);
//If you need current time in PHP, you can use this:
//$time = new DateTime(date("Y-m-d H:i:s"));
 
//Add one second,
$time->modify("+1 seconds");
 
//You can not use it as it is
//You need to convert to a string in MySQL datetime format.
$time_string = $time->format("Y-m-d H:i:s");
 
//Now you're free to use in your query
$query_new = "SELECT * FROM `table` WHERE `date` = '".$time_string."' ";
```
