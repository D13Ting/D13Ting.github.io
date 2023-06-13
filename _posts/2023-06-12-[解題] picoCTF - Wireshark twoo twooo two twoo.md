---
title: picoCTF - Wireshark twoo twooo two twoo...
date: 2023-06-12 15:11:00
categories: 
- picoCTF
tags:
- CTF
- Wireshark
- traffic analysis
---
[題目](https://play.picoctf.org/practice/challenge/110)這次也是給一個 pcap檔，還有兩句提示：

1. Did you really find _the_ flag?
2. Look for traffic that seems suspicious.

老規矩，先從Follow TCP stream來看看封包內容

**TCP stream: 5** 
**Response:** The official Red's Shrimp and Herring website is still under construction. Please check back later!

不知道在說什麼，先留著

---

**TCP stream: 6**
**Response:** `picoCTF{bfe48e8500c454d647c55a4471985e776a07b26cba64526713f43758599aa98b}`

立馬拿去提交，結果不對…那就繼續吧

---

**TCP stream: 11**
**Response:** `picoCTF{bda69bdf8f570a9aaab0e4108a0fa5f64cb26ba7d2269bb63f68af5d98b98245}`

抱持著什麼都要試試看的精神，還是錯…

---

**TCP stream: 19**
**Response:**
`picoCTF{fe83bcb6cfd43d3b79392f6a4232685f6ed4e7a789c2ce559cf3c1ab6adbe34b}`
當然也不是，後面還有很多組flag，但試了幾組都不是。也亂試了一下解碼，沒有成功。
看來不是這條路，果斷放棄。

---

隨便亂翻找到有個Response是：`AQAAANdL16XINqtaIfPEd4oSsoqp95pLlVW4Iavm0x93mlJlUw-LqQ==`，趕緊用Base64解碼看看，結果還是亂碼…

---

![](/assets/images/post/20230613_wireshark1.png)

TCP檢查過了，再來看看第二多的DNS好了。DNS的info立刻看到不尋常的資訊，每次訪問DNS的網域前面都代了一串亂碼，我決定將DNS的Info匯出看看有沒有什麼線索。

![](/assets/images/post/20230613_wireshark2.png)

將info匯出後再用Regex擷取出想要的資訊，再刪除重複項，最後全部拿去解碼。

![](/assets/images/post/20230613_wireshark3.png)

用Base64的方式解碼，但出來的項目太多了要一個一個對太困難，所以我在用utf-8去編碼，不能編的應該也不會是答案，最後程式如下：

```python
import base64
with open('domaincode.txt') as f:
    for s in f:
        tmp = base64.b64decode(s)
        try:
            tmp = base64.b64decode(s).decode("utf-8")
            print(tmp)
        except:
            pass
===========output===========
picoCT
qebͧ,
F{dns_
KɱXE
*?F("?
3xf1l_
ftw_de
,.@GM&
adbeef
}
$ΐ
```

篩選出比較有可能的答案後組合起來（多試幾次），flag就拿到啦～

`picoCTF{dns_3xf1l_ftw_deadbeef}`