---
layout: post
title:  "tryhackme - Web Enumeration"
description: 
date:   2024-03-05 19:09:00 +0800
# last_modified_at:
categories: [CTF, tryhackme]
author: d13
tags: [ctf, web exploitation, tryhackme]
---

為了準備CEHP，多熟悉考試提到的工具吧，這次主要是為了練習wpscan。這邊主要列出要解flag的解題紀錄，相關工具的教學會列在之後的CEHP工具複習那篇。

## Task 6-1

Question:  Run a directory scan on the host. Other than the standard css, images and js directories, what other directories are available?

```bash
root@ip-10-10-214-249:~# gobuster dir -u http://10.10.17.248 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.17.248
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/03/06 14:59:46 Starting gobuster
===============================================================
/images (Status: 301)
/public (Status: 301)
/css (Status: 301)
/js (Status: 301)
/Changes (Status: 301)
/VIDEO (Status: 301)
===============================================================
2024/03/06 15:00:11 Finished
===============================================================
```

Answer: public,Changes,VIDEO

## Task 6-2

Question: Run a directory scan on the host. In the "C******" directory, what file extensions exist?

```bash
root@ip-10-10-214-249:~# gobuster dir -u http://webenum.thm/Changes -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,js,css,conf,txt -t 64
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://webenum.thm/Changes
[+] Threads:        64
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     js,css,conf,txt,html
[+] Timeout:        10s
===============================================================
2024/03/06 15:06:04 Starting gobuster
===============================================================
/changes.conf (Status: 200)
/bootstrap.js (Status: 200)
```

Answer: conf,js

## Task 6-3

Question: There's a flag out there that can be found by directory scanning! Find it!

去剛剛有找到的路徑底下就可以看到一個flag檔案了，突然想到這樣6-2也不用放給gobuster跑，自己去點還比較快。

![](/assets/img/tryhackmeWebEnumeration_1.png)

Answer: thm{n1c3_w0rk}

## Task 6-4

重新複習了一下vhost，簡單來說就是可以讓同一台主機上有不同的domain或是port去服務不同的網站，從瀏覽器的角度無法得知其實是同一台主機，gobuster

```bash
root@ip-10-10-214-249:~# gobuster vhost -u http://webenum.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:          http://webenum.thm
[+] Threads:      10
[+] Wordlist:     /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
[+] User Agent:   gobuster/3.0.1
[+] Timeout:      10s
===============================================================
2024/03/06 15:17:54 Starting gobuster
===============================================================
Found: learning.webenum.thm (Status: 200) [Size: 13245]
Found: products.webenum.thm (Status: 200) [Size: 4941]
===============================================================
2024/03/06 15:18:03 Finished
===============================================================
```

Answer: learning,products

## Task 6-5

先把兩個vDomain放入到`/etc/hosts`(在本機上建立domain解析)中，然後：

```bash
root@ip-10-10-214-249:~# gobuster dir -u http://products.webenum.thm  -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://products.webenum.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt
[+] Timeout:        10s
===============================================================
2024/03/06 15:31:39 Starting gobuster
===============================================================
/css (Status: 301)
/js (Status: 301)
/flag.txt (Status: 200)
===============================================================
2024/03/06 15:32:11 Finished
===============================================================
```

Answer: thm{gobuster_is_fun}

## Task 9-1

接下來就是wpscan了~~他就是一套專門用來探測WordPress框架弱點的工具。

Question: Enumerate the site, what is the name of the theme that is detected as running?

Answer: twentynineteen

```bash
root@ip-10-10-85-8:~# echo "10.10.188.123 wpscan.thm" >> /etc/hosts 
root@ip-10-10-85-8:~# wpscan --url wpscan.thm --enumerate t 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://wpscan.thm/ [10.10.188.123]
[+] Started: Fri Mar  8 17:09:30 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://wpscan.thm/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://wpscan.thm/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://wpscan.thm/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.0 identified (Insecure, released on 2018-12-06).
 | Found By: Rss Generator (Passive Detection)
 |  - http://wpscan.thm/?feed=rss2, <generator>https://wordpress.org/?v=5.0</generator>
 |  - http://wpscan.thm/?feed=comments-rss2, <generator>https://wordpress.org/?v=5.0</generator>

[+] WordPress theme in use: twentynineteen
 | Location: http://wpscan.thm/wp-content/themes/twentynineteen/
 | Last Updated: 2023-11-07T00:00:00.000Z
 | Readme: http://wpscan.thm/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 2.7
 | Style URL: http://wpscan.thm/wp-content/themes/twentynineteen/style.css?ver=1.0
 | Style Name: Twenty Nineteen
 | Style URI: https://github.com/WordPress/twentynineteen
 | Description: A new Gutenberg-ready theme....
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://wpscan.thm/wp-content/themes/twentynineteen/style.css?ver=1.0, Match: 'Version: 1.0'

[+] Enumerating Most Popular Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:17 <================> (400 / 400) 100.00% Time: 00:00:17
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] Theme(s) Identified:

[+] twentynineteen
 | Location: http://wpscan.thm/wp-content/themes/twentynineteen/
 | Last Updated: 2023-11-07T00:00:00.000Z
 | Readme: http://wpscan.thm/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 2.7
 | Style URL: http://wpscan.thm/wp-content/themes/twentynineteen/style.css
 | Style Name: Twenty Nineteen
 | Style URI: https://github.com/WordPress/twentynineteen
 | Description: A new Gutenberg-ready theme....
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://wpscan.thm/wp-content/themes/twentynineteen/style.css, Match: 'Version: 1.0'

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Fri Mar  8 17:09:52 2024
[+] Requests Done: 830
[+] Cached Requests: 12
[+] Data Sent: 190.751 KB
[+] Data Received: 4.501 MB
[+] Memory used: 232.312 MB
[+] Elapsed time: 00:00:22

```

## Task 9-2

Question: Enumerate the site, what is the name of the plugin that WPScan has found?

Answer: nextgen-gallery

```bash
root@ip-10-10-85-8:~# wpscan --url wpscan.thm --enumerate p
Plugin(s) Identified:

[+] nextcellent-gallery-nextgen-legacy
 | Location: http://wpscan.thm/wp-content/plugins/nextcellent-gallery-nextgen-legacy/
 | Latest Version: 1.9.35 (up to date)
 | Last Updated: 2017-10-16T09:19:00.000Z
 |
 | Found By: Comment (Passive Detection)
 |
 | Version: 3.5.0 (60% confidence)
 | Found By: Comment (Passive Detection)
 |  - http://wpscan.thm/, Match: '<meta name="NextGEN" version="3.5.0"'

[+] nextgen-gallery
 | Location: http://wpscan.thm/wp-content/plugins/nextgen-gallery/
 | Last Updated: 2024-02-08T17:07:00.000Z
 | [!] The version is out of date, the latest version is 3.58
 |
 | Found By: Comment (Passive Detection)
 |
 | Version: 3.5.0 (100% confidence)
 | Found By: Comment (Passive Detection)
 |  - http://wpscan.thm/, Match: '<meta name="NextGEN" version="3.5.0"'
 | Confirmed By:
 |  Readme - Stable Tag (Aggressive Detection)
 |   - http://wpscan.thm/wp-content/plugins/nextgen-gallery/readme.txt
 |  Readme - ChangeLog Section (Aggressive Detection)
 |   - http://wpscan.thm/wp-content/plugins/nextgen-gallery/readme.txt

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

```

## Task 9-3

Question: Enumerate the site, what username can WPScan find?

Answer: Phreakazoid

## Task 9-4

Question: Construct a WPScan command to brute-force the site with this username, using the rockyou wordlist as the password list. What is the password to this user?

Answer: linkinpark

```bash
root@ip-10-10-85-8:~# wpscan --url wpscan.thm --passwords /usr/share/wordlists/rockyou.txt --usernames Phreakazoid
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://wpscan.thm/ [10.10.188.123]
[+] Started: Fri Mar  8 17:18:33 2024

Interesting Finding(s):
...Ellipsis...

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - Phreakazoid / linkinpark                                                            
Trying Phreakazoid / stupid Time: 00:00:08 <            > (505 / 14344896)  0.00%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: Phreakazoid, Password: linkinpark

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Fri Mar  8 17:18:54 2024
[+] Requests Done: 647
[+] Cached Requests: 37
[+] Data Sent: 280.716 KB
[+] Data Received: 347.363 KB
[+] Memory used: 322.691 MB
[+] Elapsed time: 00:00:20
```

## Task 12-1

Question: What is the name & version of the web server that  Nikto has determined running on **port 80?**

```bash
root@ip-10-10-150-71:~# nikto -h 10.10.24.161:80
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.10.24.161
+ Target Hostname:    ip-10-10-24-161.eu-west-1.compute.internal
+ Target Port:        80
+ Start Time:         2024-03-11 09:32:14 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.7 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x40e0 0x5a0311fe9980a 
+ The anti-clickjacking X-Frame-Options header is not present.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Allowed HTTP Methods: OPTIONS, GET, HEAD, POST 
+ OSVDB-3092: /sitemap.xml: This gives a nice listing of the site content.
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3268: /images/?pattern=/etc/*&sort=name: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 6544 items checked: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2024-03-11 09:32:26 (GMT0) (12 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

Answer: Apache/2.4.7

## Task 12-2

Question: There is another web server running on another port. What is the name & version of this web server?

先用Nmap掃描一下有開的port，再用瀏覽器打開看一下是不是網站

```bash
root@ip-10-10-150-71:~# nmap 10.10.24.161

Starting Nmap 7.60 ( https://nmap.org ) at 2024-03-11 09:34 GMT
Nmap scan report for ip-10-10-24-161.eu-west-1.compute.internal (10.10.24.161)
Host is up (0.00051s latency).
Not shown: 989 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
1090/tcp open  ff-fms
1091/tcp open  ff-sm
1098/tcp open  rmiactivation
1099/tcp open  rmiregistry
4446/tcp open  n1-fwp
5500/tcp open  hotline
8009/tcp open  ajp13
8080/tcp open  http-proxy
8083/tcp open  us-srv
MAC Address: 02:C2:DA:6F:E0:69 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.78 seconds
root@ip-10-10-150-71:~# nikto -h 10.10.24.161:8080
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.10.24.161
+ Target Hostname:    ip-10-10-24-161.eu-west-1.compute.internal
+ Target Port:        8080
+ Start Time:         2024-03-11 09:35:31 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache-Coyote/1.1
+ Retrieved x-powered-by header: Servlet/3.0; JBossAS-6
+ Server leaks inodes via ETags, header found with file /, fields: 0xW/1554 0x1313480882000 
+ The anti-clickjacking X-Frame-Options header is not present.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ OSVDB-39272: favicon.ico file identifies this server as: JBoss Server
+ Allowed HTTP Methods: GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS 
+ OSVDB-397: HTTP method ('Allow' Header): 'PUT' method could allow clients to save files on the web server.
+ OSVDB-5646: HTTP method ('Allow' Header): 'DELETE' may allow clients to remove files on the web server.
+ Cookie JSESSIONID created without the httponly flag
+ 6544 items checked: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2024-03-11 09:35:56 (GMT0) (25 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

Answer: Apache-Coyote/1.1

## Task 12-3

Question: What is the name of the Cookie that this JBoss server gives?

```bash
root@ip-10-10-150-71:~# nikto -h 10.10.24.161:8080 -Display 2
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.10.24.161
+ Target Hostname:    ip-10-10-24-161.eu-west-1.compute.internal
+ Target Port:        8080
+ Start Time:         2024-03-11 09:39:06 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache-Coyote/1.1
+ Retrieved x-powered-by header: Servlet/3.0; JBossAS-6
+ Server leaks inodes via ETags, header found with file /, fields: 0xW/1554 0x1313480882000 
+ The anti-clickjacking X-Frame-Options header is not present.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ OSVDB-39272: favicon.ico file identifies this server as: JBoss Server
+ Allowed HTTP Methods: GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS 
+ OSVDB-397: HTTP method ('Allow' Header): 'PUT' method could allow clients to save files on the web server.
+ OSVDB-5646: HTTP method ('Allow' Header): 'DELETE' may allow clients to remove files on the web server.
+ /admin-console/config.php sent cookie: JSESSIONID=D83C9FCC231DE48FE149221C67C8D692; Path=/admin-console
+ Cookie JSESSIONID created without the httponly flag
+ /jmx-console/HtmlAdaptor?action=inspectMBean&name=Catalina%3Atype%3DServer sent cookie: JSESSIONID=75881F9A223D612FCD84935A589AE99F; Path=/jmx-console
+ 6544 items checked: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2024-03-11 09:39:18 (GMT0) (12 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

Answer: JSESSIONID