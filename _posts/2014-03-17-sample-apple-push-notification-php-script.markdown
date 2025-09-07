---
id: 55
title: 'PHP script for sending iOS push notifications'
date: '2014-03-17T20:54:24+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=55'
permalink: /sample-apple-push-notification-php-script/
categories:
    - 'Blog Posts'
tags:
    - iOS
    - PHP
---

This is an old script that I used to use for sending notifications. I don’t remember the source, on the other hand, it has been modified a couple of times already.

Even though I could manage to send notifications with this script, the Apple server was rejecting the connection after the amount reaches more than 50. It was usually getting cut off around 70. So it needs to get some management for not sending in a loop, like threading or whatever.

```
<?php
 
// set time limit to zero in order to avoid timeout
set_time_limit(0);
 
// charset header for output
header('content-type: text/html; charset: utf-8');
 
// this is the pass phrase you defined when creating the key
$passphrase = 'my_secret_pass';
 
// you can post a variable to this string or edit the message here
if (!isset($_POST['msg'])) {
$_POST['msg'] = "Notification message here!";
}
 
// tr_to_utf function needed to fix the Turkish characters
$message = tr_to_utf($_POST['msg']);
 
// load your device ids to an array
$deviceIds = array(
'lh142lk3h1o2141p2y412d3yp1234y1p4y1d3j4u12p43131p4y1d3j4u12p4313',
'y1p4y1d3j4u12p43131p4y1d3j4u12p4313lh142lk3h1o2141p2y412d3yp1234'
);
 
// this is where you can customize your notification
$payload = '{"aps":{"alert":"' . $message . '","sound":"default"}}';
 
$result = 'Start' . '<br />';
 
////////////////////////////////////////////////////////////////////////////////
// start to create connection
$ctx = stream_context_create();
stream_context_set_option($ctx, 'ssl', 'local_cert', 'MyAppGenerated.pem');
stream_context_set_option($ctx, 'ssl', 'passphrase', $passphrase);
 
echo count($deviceIds) . ' devices will receive notifications.<br />';
 
foreach ($deviceIds as $item) {
    // wait for some time
    sleep(1);
     
    // Open a connection to the APNS server
    $fp = stream_socket_client('ssl://gateway.push.apple.com:2195', $err, $errstr, 60, STREAM_CLIENT_CONNECT | STREAM_CLIENT_PERSISTENT, $ctx);
 
    if (!$fp) {
        exit("Failed to connect: $err $errstr" . '<br />');
    } else {
        echo 'Apple service is online. ' . '<br />';
    }
 
    // Build the binary notification
    $msg = chr(0) . pack('n', 32) . pack('H*', $item) . pack('n', strlen($payload)) . $payload;
     
    // Send it to the server
    $result = fwrite($fp, $msg, strlen($msg));
     
    if (!$result) {
        echo 'Undelivered message count: ' . $item . '<br />';
    } else {
        echo 'Delivered message count: ' . $item . '<br />';
    }
 
    if ($fp) {
        fclose($fp);
        echo 'The connection has been closed by the client' . '<br />';
    }
}
 
echo count($deviceIds) . ' devices have received notifications.<br />';
 
// function for fixing Turkish characters
function tr_to_utf($text) {
    $text = trim($text);
    $search = array('Ü', 'Þ', 'Ð', 'Ç', 'Ý', 'Ö', 'ü', 'þ', 'ð', 'ç', 'ý', 'ö');
    $replace = array('Ãœ', 'Åž', '&#286;ž', 'Ã‡', 'Ä°', 'Ã–', 'Ã¼', 'ÅŸ', 'ÄŸ', 'Ã§', 'Ä±', 'Ã¶');
    $new_text = str_replace($search, $replace, $text);
    return $new_text;
}
 
// set time limit back to a normal value
set_time_limit(30);
```
