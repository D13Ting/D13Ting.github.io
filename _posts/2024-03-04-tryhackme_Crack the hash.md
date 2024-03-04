---
layout: post
title:  "tryhackme - Crack the hash"
description: 
date:   2024-03-04 10:09:00 +0800
# last_modified_at:
categories: [CTF, tryhackme]
author: d13
tags: [ctf, cryptography, tryhackme, hash]
---

準備CEHP，練一下工具。

## Task 1-1

Question: 48bb6e862e54f2a795ffc4e541caed4d

都先用這個網站解解看，不行再用工具：[https://crackstation.net/](https://crackstation.net/)

Answer: password123

![](/assets/img/tryhackmeCrackthehash_1.png)

## Task 1-2

一樣用上面的工具解開

Question: CBFDAC6008F9CAB4083784CBD1874F76618D2A97

Answer: password123

## Task 1-3

一樣用上面的工具解開

Question: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

Answer: letmeindw

## Task 1-4

Question: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

這一題的比較麻煩，看個提示：

*Search the hashcat examples [page](https://hashcat.net/wiki/doku.php?id=example_hashes) for $2y$. This type of hash can take a very long time to crack, so either filter rockyou for four character words, or use a mask for four lower case alphabetical characters.*

先把rockyou.txt裡面四個小寫字元的整理出來：

```bash
┌─[parrot@parrot]─[~/Desktop]
└──╼ $grep -x -E '[a-z][^0-9]{3}' rockyou.txt > candidates.txt
```

然後提示說要去hashcat找是哪一種加密的方法，研究了一下`$2y$`到底甚麼意思，終於找到是以下這條：

| Hash-Mode | Hash-Name | Example |
| --- | --- | --- |
| 3200 | bcrypt $2*$, Blowfish (Unix) | $2a$05$LhayLxezLhK1LhWvKxCyLOj0j1u.Kj0jZ0pEmm134uzrQlFvQJLF6 |

接著使用HashCat工具：

```bash
┌─[parrot@parrot]─[~/Desktop]
└──╼ $hashcat -a 0 -m 3200 pass.txt candidates.txt
hashcat (v6.1.1) starting...

You have enabled --force to bypass dangerous warnings and errors!
This can hide serious problems and should only be done when debugging.
Do not report hashcat issues encountered when using --force.
OpenCL API (OpenCL 1.2 pocl 1.6, None+Asserts, LLVM 9.0.1, RELOC, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=============================================================================================================================
* Device #1: pthread-11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz, 5806/5870 MB (2048 MB allocatable), 8MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 72

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 66 MB

Dictionary cache built:
* Filename..: candidates.txt
* Passwords.: 8663
* Bytes.....: 43507
* Keyspace..: 8663
* Runtime...: 0 secs

$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom:bleh
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: bcrypt $2*$, Blowfish (Unix)
Hash.Target......: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX...8wsRom
Time.Started.....: Mon Mar  4 07:55:05 2024, (38 secs)
Time.Estimated...: Mon Mar  4 07:55:43 2024, (0 secs)
Guess.Base.......: File (candidates.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       13 H/s (8.18ms) @ Accel:1 Loops:64 Thr:1 Vec:16
Recovered........: 1/1 (100.00%) Digests
Progress.........: 496/8663 (5.73%)
Rejected.........: 0/496 (0.00%)
Restore.Point....: 488/8663 (5.63%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4032-4096
Candidates.#1....: bleh -> tino

Started: Mon Mar  4 07:55:03 2024
Stopped: Mon Mar  4 07:55:45 2024
```

hashcat的參數`-a`代表的是攻擊模式，`-m`這是雜湊模式，就是剛剛查到的3200，`-a`的值可以參考下面：

```bash
- [ Attack Modes ] -
  # | Mode
 ===+======
  0 | Straight
  1 | Combination
  3 | Brute-force
  6 | Hybrid Wordlist + Mask
  7 | Hybrid Mask + Wordlist
```

Answer: bleh

## Task 1-5

一樣用上面的工具解開

Question: 279412f945939ba78ce0758d3fd83daa

Answer: Eternity22

## Task 2-1

Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

一樣用 [https://crackstation.net/](https://crackstation.net/) 直接解開

Answer: paule

## Task 2-2

Hash: 1DFECA0C002AE40B8619ECF94819CC1B

Hint: NTLM

同上

Answer: n63umy8lkf4i

## Task 2-3

Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Salt: aReallyHardSalt

這一題感覺也比較麻煩，找了關於`$6$`的mode：

| Hash-Mode | Hash-Name | Example |
| --- | --- | --- |
| 1800 | sha512crypt $6$, SHA512 (Unix) 2 | $6$52450745$k5ka2p8bFuSmoVT1tzOyyuaREkkKBcCNqoDKzYiJL9RaE8yMnPgh2XzzF0NDrUhgrcLwg78xs1w5pJiypEdFX/ |

接下來一樣用hashcat，就使用rockyou.txt先解解看，花了一個小時才解好：

```bash
┌─[✗]─[parrot@parrot]─[~/Desktop]
└──╼ $hashcat -m 1800 pass.txt rockyou.txt
hashcat (v6.1.1) starting...

OpenCL API (OpenCL 1.2 pocl 1.6, None+Asserts, LLVM 9.0.1, RELOC, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=============================================================================================================================
* Device #1: pthread-11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz, 5806/5870 MB (2048 MB allocatable), 8MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt
* Uses-64-Bit

ATTENTION! Pure (unoptimized) backend kernels selected.
Using pure kernels enables cracking longer passwords but for the price of drastically reduced performance.
If you want to switch to optimized backend kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 66 MB

Dictionary cache built:
* Filename..: rockyou.txt
* Passwords.: 14344391
* Bytes.....: 139921497
* Keyspace..: 14344384
* Runtime...: 2 secs

$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:waka99
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPM...ZAs02.
Time.Started.....: Mon Mar  4 09:43:37 2024 (33 mins, 26 secs)
Time.Estimated...: Mon Mar  4 10:17:03 2024 (0 secs)
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     1412 H/s (9.05ms) @ Accel:256 Loops:32 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 2832384/14344384 (19.75%)
Rejected.........: 0/2832384 (0.00%)
Restore.Point....: 2830336/14344384 (19.73%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4992-5000
Candidates.#1....: walbin1 -> waitete

Started: Mon Mar  4 09:42:42 2024
Stopped: Mon Mar  4 10:17:04 2024
```

Answer: waka99

## Task 2-4

Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Salt: tryhackme

解完上面的這題應該不會太難，一樣先找到他的Hash-Mode

| Hash-Mode | Hash-Name | Example |
| --- | --- | --- |
| 160 | HMAC-SHA1 (key = $salt) | d89c92b4400b15c39e462a8caa939ab40c3aeeea:1234 |

接下來就是用hashcat找了，注意這一題你要把salt放在雜湊後面：

```bash
┌─[parrot@parrot]─[~/Desktop]
└──╼ $echo e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme > pass.txt 
┌─[parrot@parrot]─[~/Desktop]
└──╼ $cat pass.txt 
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme
┌─[parrot@parrot]─[~/Desktop]
└──╼ $hashcat -m 160 pass.txt rockyou.txt
hashcat (v6.1.1) starting...

OpenCL API (OpenCL 1.2 pocl 1.6, None+Asserts, LLVM 9.0.1, RELOC, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=============================================================================================================================
* Device #1: pthread-11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz, 5806/5870 MB (2048 MB allocatable), 8MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Using pure kernels enables cracking longer passwords but for the price of drastically reduced performance.
If you want to switch to optimized backend kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 66 MB

Dictionary cache hit:
* Filename..: rockyou.txt
* Passwords.: 14344384
* Bytes.....: 139921497
* Keyspace..: 14344384

e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:481616481616
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: HMAC-SHA1 (key = $salt)
Hash.Target......: e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme
Time.Started.....: Mon Mar  4 15:56:29 2024 (7 secs)
Time.Estimated...: Mon Mar  4 15:56:36 2024 (0 secs)
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1827.9 kH/s (2.08ms) @ Accel:1024 Loops:1 Thr:1 Vec:16
Recovered........: 1/1 (100.00%) Digests
Progress.........: 12320768/14344384 (85.89%)
Rejected.........: 0/12320768 (0.00%)
Restore.Point....: 12312576/14344384 (85.84%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 48162440 -> 47784sm

Started: Mon Mar  4 15:56:09 2024
Stopped: Mon Mar  4 15:56:36 2024
```

Answer: 481616481616

這個lab算是蠻簡單的，主要是熟悉工具而已，之後有空再來挑戰它的level 2

[https://tryhackme.com/room/crackthehashlevel2](https://tryhackme.com/room/crackthehashlevel2)
