---
layout: post
title:  "picoCTF - Mind your Ps and Qs"
description: 
date:   2023-12-20 15:58:00 +0800
# last_modified_at:
categories: [CTF, picoCTF-Cryptography]
author: d13
tags: [ctf, cryptography, picoctf]
---

# Description

In RSA, a small `e` value can be problematic, but what about `N`? Can you decrypt this? [values](https://mercury.picoctf.net/static/3cfeb09681369c26e3f19d886bc1e5d9/values)

# Hints

Bits are expensive, I used only a little bit over 100 to save money

# **Solution**

```
Decrypt my super sick RSA:
c: 8533139361076999596208540806559574687666062896040360148742851107661304651861689
n: 769457290801263793712740792519696786147248001937382943813345728685422050738403253
e: 65537
```

題目給了N跟e，透過[factordb](http://factordb.com/index.php)可以直接將一些已知的N做分解，得到：

```
p = 1617549722683965197900599011412144490161
q = 475693130177488446807040098678772442581573
```

寫了一支python做解密，就可以得到答案了

```python
from Crypto.Util.number import inverse, long_to_bytes

c = 8533139361076999596208540806559574687666062896040360148742851107661304651861689
N = 769457290801263793712740792519696786147248001937382943813345728685422050738403253
e = 65537
p = 1617549722683965197900599011412144490161
q = 475693130177488446807040098678772442581573
phi = (p-1)*(q-1)
d = inverse(e, phi)
m = pow(c, d, N)

print(long_to_bytes(m))
```

# Flag

picoCTF{sma11_N_n0_g0od_45369387}