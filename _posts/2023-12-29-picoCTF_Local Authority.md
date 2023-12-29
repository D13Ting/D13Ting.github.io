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

進去就是一個login畫面，看了一下原始碼，沒有什麼資訊。但只要嘗試登入看看就會看到驗證的JS被載入，可以直接看到帳號密碼的明文。登入後即可以取得flag
![](../img/Local_Authority.png)

## Flag

picoCTF{j5_15_7r4n5p4r3n7_05df90c8}