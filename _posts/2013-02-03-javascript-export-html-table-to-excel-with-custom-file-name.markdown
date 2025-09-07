---
id: 38
title: 'Exporting HTML table to Excel in JavaScript'
date: '2013-02-03T20:29:00+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=38'
permalink: /javascript-export-html-table-to-excel-with-custom-file-name/
categories:
    - 'Blog Posts'
tags:
    - JavaScript
---

Web browser wars hurt developers’ hearts. It is mostly about CSS, sometimes about JavaScript, HTML etc…

Thus I know how it feels when you expect something to get done easily appears a lot more harder than you expected.

Code below is tested only with Chrome (24+). It is making these processes:

- Gets the HTML code of your table inside your div element.
- Replaces the spaces in the code with correct syntax for Excel (otherwise spaces will be removed in your Excel sheet).
- Generates a specific file name (for minutes) in order to avoid overriding old files and to supply archiving by date values.
- And lastly and most importantly, saving the file with a custom file name.

Here’s my combination of codes (from different SO questions or tutorials) if you want to save HTML table to client computer using JavaScript code.

```
$(document).ready(function() {
    $("#btnExport").click(function(e) {
        //getting values of current time for generating the file name
        var dt = new Date();
        var day = dt.getDate();
        var month = dt.getMonth() + 1;
        var year = dt.getFullYear();
        var hour = dt.getHours();
        var mins = dt.getMinutes();
        var postfix = day + "." + month + "." + year + "_" + hour + "." + mins;
        //creating a temporary HTML link element (they support setting file names)
        var a = document.createElement('a');
        //getting data from our div that contains the HTML table
        var data_type = 'data:application/vnd.ms-excel';
        var table_div = document.getElementById('dvData');
        var table_html = table_div.outerHTML.replace(/ /g, '%20');
        a.href = data_type + ', ' + table_html;
        //setting the file name
        a.download = 'exported_table_' + postfix + '.xls';
        //triggering the function
        a.click();
        //just in case, prevent default behaviour
        e.preventDefault();
    });
});
```
