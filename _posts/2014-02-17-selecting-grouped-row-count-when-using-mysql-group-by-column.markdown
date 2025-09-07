---
id: 47
title: 'Getting grouped row count in MySQL'
date: '2014-02-17T20:43:24+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=47'
permalink: /selecting-grouped-row-count-when-using-mysql-group-by-column/
categories:
    - 'Blog Posts'
tags:
    - SQL
---

You may have been using subqueries to get how many rows were grouped when using MySQL’s GROUP BY function. There’s a little trick that helps to get this number without using subqueries, more clean and faster (when the amount of rows get higher).

Let’s assume we have a table contain folks from different cities. Like this:

```
folk_name   |   city_name
---------------------------
evren       |   ohio
steve       |   ohio
cem         |   weston
don         |   sunderland
ritchie     |   weston
ian         |   hounslow
alican      |   ohio
```

When we want to get only city names (distinct) from this table, we may group the data by city\_name column like this:

```
SELECT f.* FROM `folks` f GROUP BY f.`city_name`
```

And the result would be:

```
folk_name   |   city_name
---------------------------
ian         |   hounslow
evren       |   ohio
don         |   sunderland
cem         |   weston
```

The expected behaviour of GROUP BY is to select first rows so other folks from same cities do not appear. It also orders by the GROUP BY column (ascending).

Let’s say we want to get how many folks exist in each city by grouping by. First thing comes to mind could be using a subquery like this:

```
SELECT f1.*, (SELECT COUNT(f2.`folk_name`) FROM `folks` f2 WHERE f2.`city_name` = f1.`city_name`) AS `folk_count` FROM `folks` f1 GROUP BY `city_name`
```

And the result would be:

```
folk_name   |   city_name   |   folk_count
-------------------------------------------
ian         |   hounslow    |   1
evren       |   ohio        |   3
don         |   sunderland  |   1
cem         |   weston      |   2
```

Let’s admit, subqueries are messy. They make the query more complicated to read and also are question marks on performance.

How about selecting number 1 (one) for each row and sum it up on grouping by?

Doing something like this:

```
SELECT f.*,SUM(1) AS `folk_count` FROM `folks` f GROUP BY f.`city_name`
```

Would return:

```
folk_name   |   city_name   |   folk_count
-------------------------------------------
ian         |   hounslow    |   1
evren       |   ohio        |   3
don         |   sunderland  |   1
cem         |   weston      |   2
```

Nicely done.
