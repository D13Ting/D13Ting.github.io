---
layout: post
title:  "picoCTF - Local Authority"
description: 
date:   2023-12-29 13:06:00 +0800
# last_modified_at:
categories: [CTF, picoCTF-Web Exploitation]
author: d13
tags: [ctf, web exploitation, picoctf]
---

## Question

[https://play.picoctf.org/practice/challenge/278](https://play.picoctf.org/practice/challenge/278)

## Solution

網頁的原始碼：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="style.css">
    <title>On Includes</title>
  </head>
  <body>
    <script src="script.js"></script>
  
    <h1>On Includes</h1>
    <p>Many programming languages and other computer files have a directive, 
       often called include (sometimes copy or import), that causes the 
       contents of a second file to be inserted into the original file. These 
       included files are called copybooks or header files. They are often used
       to define the physical layout of program data, pieces of procedural code
       and/or forward declarations while promoting encapsulation and the reuse
       of code.</p>
    <br>
    <p> Source: Wikipedia on Include directive </p>
    <button type="button" onclick="greetings();">Say hello</button>
  </body>
</html>
```

網站上簡單說明include是什麼東西，給了很大的提示，在這個網頁中include了`style.css`跟`script.js`兩支程式，都點開來看註解就可以找到答案了

## Flag

picoCTF{1nclu51v17y_1of2_f7w_2of2_b8f4b022}