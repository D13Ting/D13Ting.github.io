---
layout: post
title:  "picoCTF - la cifra de"
description: 
date:   2023-12-20 15:58:00 +0800
# last_modified_at:
categories: [CTF, picoCTF-Cryptography]
author: d13
tags: [ctf, cryptography, picoctf]
---

## Question

https://play.picoctf.org/practice/challenge/3

## Solution

從文章內容推得出來是一種字母變換的加密方法，而且可能加密的key長度不長（憑直覺，只是覺得一些單詞的字母沒有錯哈哈哈哈），那應該就是[vigenère cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)，這裡推薦在網路上看到的專門破這種加密法的工具[Vigenère Solver](https://www.guballa.de/vigenere-solver)，只要將文章丟進去，他就會幫你破解出使用的key跟還原原文。

## Flag

picoCTF{b311a50_0r_v1gn3r3_c1ph3r6fe60eaa}