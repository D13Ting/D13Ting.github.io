# picoCTF - PcapPoisoning

# Description

How about some hide and seek heh?Download this [file](https://artifacts.picoctf.net/c/377/trace.pcap) and find the flag.

# **Solution**

給了一包封包檔，用WireShark打開來看看，大部分都TCP跟FTP，沒有什麼頭緒。follow TCP stream 也看不出什麼線索，最後想說用filter篩選含有picoCTF的字樣，沒想到就這樣中了！

![](../img/PcapPoisoning_1.png)

# Flag

picoCTF{P64P_4N4L7S1S_SU55355FUL_31010c46}