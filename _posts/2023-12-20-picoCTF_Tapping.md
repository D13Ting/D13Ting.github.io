---
layout: post
title:  "picoCTF - Tapping"
description: 
date:   2023-12-20 15:58:00 +0800
# last_modified_at:
categories: [CTF, picoCTF-Cryptography]
author: d13
tags: [ctf, cryptography, picoctf]
---

# Description

Theres tapping coming in from the wires. What's it saying `nc jupiter.challenges.picoctf.org 48247`.

# Hints

1. What kind of encoding uses dashes and dots?
2. The flag is in the format PICOCTF{}

# **Solution**

```
.--. .. -.-. --- -.-. - ..-. { -- ----- .-. ... ...-- -.-. ----- -.. ...-- .---- ... ..-. ..- -. .---- ..--- -.... .---- ....- ...-- ---.. .---- ---.. .---- }
```

會這樣有點有條的我只知道摩斯密碼，上網用[工具](https://codepen.io/hdsbook/full/rNOBrdL)轉換就好。

# Flag

picoCTF{b311a50_0r_v1gn3r3_c1ph3r6fe60eaa}