---
title: [解題] picoCTF - Wireshark doo dooo do doo...
date: 2023-06-06 14:35:00
categories: 
- picoCTF
tags:
- CTF
- Wireshark
- traffic analysis
---
剛考過CEH，接下來決定多練習CTF增進滲透的實作能力！目前打算先從[picoCTF](https://picoctf.org/)開始。
這次練習的[題目](https://play.picoctf.org/practice/challenge/115)只給了一包Pcap檔，把它丟進[WireShark](https://www.wireshark.org/)看看。大部分都是 TCP 跟 HTTP 協定的封包來回，先使用分析工具(Analyze→Follow→TCP Stream)查看一下傳送的內容。

>💡 Follow Stream 的功能可以依序追蹤封包所傳送的內容，將有關聯性的封包合併呈現，使用者可以更方便解讀封包的內容訊息

![](/assets/images/post/20230607_wireshark1.png)

隨意檢查一下，在第五個TCP Stream看到一組GET Method，Response中也有回傳一串亂碼：

`Gur synt vf cvpbPGS{c33xno00_1_f33_h_qrnqorrs}`

![](/assets/images/post/20230607_wireshark2.png)

pico的flag格式就是：`picoCTF{flag}`，所以可以猜測這串亂碼可能是flag，而且是用無法轉換大括號的方式加密，再來注意到亂碼cvpb的c對應到明碼pico的p；而p則是對應到c，那應該是透過替換式加密法 ROT13 加密

![](/assets/images/post/20230607_rot13.png){:width="300px"}

使用[加解密工具](https://gchq.github.io/CyberChef/)就可以看到flag了
`picoCTF{p33kab00_1_s33_u_deadbeef}`