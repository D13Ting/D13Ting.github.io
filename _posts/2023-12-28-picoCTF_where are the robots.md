---
layout: post
title:  "picoCTF - where are the robots"
description: 
date:   2023-12-28 18:10:00 +0800
# last_modified_at:
categories: [CTF, picoCTF-Web Exploitation]
author: d13
tags: [ctf, web exploitation, picoctf]
---

## Question

[https://play.picoctf.org/practice/challenge/4](https://play.picoctf.org/practice/challenge/4)

## Solution

進來就是一個網頁，其實題目給的提示太明顯了，就是要我們找robots.txt這個檔案。

> The posts' _layout_ has been set to `post` by default, so there is no need to add the variable _layout_ in the Front Matter block.
{: .prompt-tip }

![](/assets/img/wherearetherobots_1.png)

在根目錄直接加上檔案名稱就可以看到這個文件了，那文件裡有一個不允許爬蟲的連結為/1bb4c.html，連上去看看。

![](/assets/img/wherearetherobots_2.png)

樣就找到了！

![](/assets/img/wherearetherobots_3.png)

# Flag

picoCTF{ca1cu1at1ng_Mach1n3s_1bb4c}