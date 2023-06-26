# picoCTF - Matryoshka doll

# Description

Matryoshka dolls are a set of wooden dolls of decreasing size placed one inside another. What's the final one? Image: [this](https://mercury.picoctf.net/static/f6cc2560a70b1ea811c151accba5390f/dolls.jpg)

# Hints

1. Wait, you can hide files inside files? But how do you find them?
2. Make sure to submit the flag as picoCTF{XXXXX}

# **Solution**

題目只給你一張俄羅斯娃娃的圖檔作為線索。首先，使用binwalk對這張圖片掃描一次，它會自動幫你掃描這個檔案裡有哪些內容，我們可以發現在圖片的結尾其實還有包含一個壓縮檔。

```bash
$ binwalk dolls.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 594 x 1104, 8-bit/color RGBA, non-interlaced
3226          0xC9A           TIFF image data, big-endian, offset of first image directory: 8
272492        0x4286C         Zip archive data, at least v2.0 to extract, compressed size: 378955, uncompressed size: 383936, name: base_images/2_c.jpg
651613        0x9F15D         End of Zip archive, footer length: 22
```

接著我們使用 unzip 把 dolls.jpg 中隱藏的壓縮檔解壓縮後，發現裡面還是一張圖片。

```bash
$ unzip dolls.jpg
Archive:  dolls.jpg
warning [dolls.jpg]:  272492 extra bytes at beginning or within zipfile
  (attempting to process anyway)
  inflating: base_images/2_c.jpg
```

其實這題就像是俄羅斯娃娃一樣，一個包一個，必須要不斷解壓縮到最後才能找到 flag，這邊 binwalk 又派上用場了，使用binwalk並加上參數`-Me`就可以自動將檔案中隱藏的東西通通提取出來。

```bash
~/base_images$ binwalk -Me 2_c.jpg 

Scan Time:     2023-05-11 16:51:56
Target File:   base_images/2_c.jpg
MD5 Checksum:  736e7ba2c359bb3e1e69e6f1f812548f
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 526 x 1106, 8-bit/color RGBA, non-interlaced
3226          0xC9A           TIFF image data, big-endian, offset of first image directory: 8
187707        0x2DD3B         Zip archive data, at least v2.0 to extract, compressed size: 196041, uncompressed size: 201443, name: base_images/3_c.jpg
383803        0x5DB3B         End of Zip archive, footer length: 22
383914        0x5DBAA         End of Zip archive, footer length: 22

Scan Time:     2023-05-11 16:51:57
Target File:   base_images/_2_c.jpg.extracted/base_images/3_c.jpg
MD5 Checksum:  63c6dd19e06a525ca7748efb25e25d2a
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 428 x 1104, 8-bit/color RGBA, non-interlaced
3226          0xC9A           TIFF image data, big-endian, offset of first image directory: 8
123606        0x1E2D6         Zip archive data, at least v2.0 to extract, compressed size: 77649, uncompressed size: 79806, name: base_images/4_c.jpg
201421        0x312CD         End of Zip archive, footer length: 22

Scan Time:     2023-05-11 16:51:57
Target File:   base_images/_2_c.jpg.extracted/base_images/_3_c.jpg.extracted/base_images/4_c.jpg
MD5 Checksum:  f5d6128e569b61bad3fbbe3891b52188
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 320 x 768, 8-bit/color RGBA, non-interlaced
3226          0xC9A           TIFF image data, big-endian, offset of first image directory: 8
79578         0x136DA         Zip archive data, at least v2.0 to extract, compressed size: 62, uncompressed size: 81, name: flag.txt
79784         0x137A8         End of Zip archive, footer length: 22

Scan Time:     2023-05-11 16:51:57
Target File:   base_images/_2_c.jpg.extracted/base_images/_3_c.jpg.extracted/base_images/_4_c.jpg.extracted/flag.txt
MD5 Checksum:  a44bad1293786441ce4683a9682c90bf
Signatures:    411
```

最後就成功拿到flag.txt啦～

# Flag

picoCTF{ac0072c423ee13bfc0b166af72e25b61}