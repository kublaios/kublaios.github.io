---
id: 32
title: 'Concatenating multiple SQL rows into one cell'
date: '2012-07-02T20:24:00+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=32'
permalink: /t-sql-concatenate-multiple-rows-of-a-column-into-one-cell/
categories:
    - 'Blog Posts'
tags:
    - SQL
---

Simple: You have a column and you want to take all rows as one cell. So that you can do searching, ordering, whatever you like.

Solution: Declare a variable, in this case VARCHAR or NVARCHAR, then SELECT the variable by equaling \[itself\] plus \[column name\] plus \[your seperation char\].

Here’s the code snippet:

```
DECLARE @concatenated NVARCHAR(1000)
SET @concatenated = ''
SELECT @concatenated = @concatenated + [NAME_COLUMN] + ',' FROM [USERS_TABLE] WHERE [ID_COLUMN] < 100
SELECT @concatenated
```
