---
layout: post
title:  "Content-Security-Policy 介紹"
description: 
date:   2024-01-21 18:22:00 +0800
# last_modified_at:
categories: [Web Security]
author: d13
tags: [http header, XSS, web security]
---

內容安全政策（Content Security Policy，簡稱CSP）是一種安全標準，主要是防止XSS、clickjacking之類的攻擊。大部分的瀏覽器都支援這一套標準。CSP可以直接加在前端網頁上，也可以透過網站伺服器去設置。

## CSP的功能

- 來源白名單
- 來源格式白名單
- 禁用inline
- 禁用eval()

### 來源白名單

透過設置CSP可以指定瀏覽器只能夠載入來自白名單內的資源，以防止攻擊者從外部載入惡意程式。以加入至HTML為例：

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'">
```

只要在HTML中加入以上這句，就可以限制除了與自身相同domain的資源（`self`）之外皆不載入，以此避免惡意程式的注入。 其中的`default-src`代表要限制的html tag，如果今天有一支外部JS需要被導入，那就可以先用`default-src`限制所有tag後再加上`script-src`的設定，如下方程式碼：

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src <https://example.com>">
```

如上所示，後面的政策不會被前面的限制，所以`script-src`就會有`self`和`https://example.com`兩個來源可以載入，除了`default-src`和`script-src`之外，還有其他不同的tag可以設置，以下列出一些常見的：

- `default-src`
- `frame-src`
- `img-src`
- `script-src`
- `style-src`

這邊建議一律都先加入`default-src 'self'`，其他有需要的再加入即可。

### 來源格式白名單

雖然設置了來源的白名單，但還是有可能再載入的過程中被中間人替換掉，所以再加入這個功能防止。這個功能可以在政策上去規範導入的資源一定要走`https`，範例如下：

```html
<meta http-equiv="Content-Security-Policy" content="script-src <https://example.com>" https:>
```

除了規定`https`之外，還有以下可以設置：

- `http:`
- `https:`
- `data:`
- `mediastream:`
- `blob:`
- `filesystem:`

### 禁用inline

在做網站開發的時候，時常會將javascript直接寫在html裡面，但是攻擊者也時常將惡意程式碼注入在html上以達到目的。為了防範這類型的攻擊，CSP直接禁止在檔案內使用script，只能夠從外部引用。 若是希望在使用CSP的同時也可以直接執行html內的script，則需要在CSP政策上加上`unsafe-inline`，如下方程式碼：

```html
<meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline'">
```

若希望在使用`unsafe-inline`的同時以可以維持其安全性的話，CSP也提供兩種做法：

1. 加入script的hash值 支援將script經過sha256, sha384, sha512後再base64的值，只要將該值也加入至`script-src`底下便可以使用檔案內的這部分script，而攻擊者再注入程式碼後也會因為雜湊值不符而被CSP擋下來。
2. 加入nonce 利用後端產生一組隨機的nonce再加入至CSP裡面和網頁的script裡面，讓CSP知道這段script是開發者所撰寫而非攻擊者，加入後程式碼如下：

```html
<meta http-equiv="Content-Security-Policy" content="script-src 'self' 'nonce-iufhasdnjasd3ijas=';">

<!-- inline的script tag加入以下參數 -->
<script nonce="iufhasdnjasd3ijas="></script>
```

### 禁用eval()

很多開發者為求方便，會使用eval()直接將字串當成程式碼執行。越方便的是就越不安全，CSP當然也禁止了這項功能，如果希望開放，則要加上`unsafe-eval` 。

## CSP檢測工具

推薦以下兩款線上工具，可以幫助開發者確認自己的CSP政策是否完善：



1. Google推出的，可以幫你檢查CSP指令是否安全

   [https://csp-evaluator.withgoogle.com/](https://csp-evaluator.withgoogle.com/)

2. 將欲檢測的網址丟上去就會幫你是否有漏掉哪些header（注意這會成為公開資訊）

   [https://securityheaders.com/](https://securityheaders.com/)

## Demo

我找了一個GitHub上的XSS範例網站https://github.com/bgres/xss-demo架設在IIS上。在這個網站的Search query欄位輸入資料並送出，便會將輸入顯示在網頁上，這邊我輸入了一個test：

![](/assets/img/Content-Security-Policy_1.png)

那如果輸入`<script>alert(1)</script>`便可以觸發彈跳視窗：

![](/assets/img/Content-Security-Policy_2.png)

若要阻擋XSS，最基本的就是要針對輸入輸出做驗證與過濾。但若開發設計一開始沒有規劃好，或是流程太衝促，時常會有忘記做檢查的狀況。這種時候其實就可以將CSP加入至伺服器的response標頭，瀏覽器在接受後就會自動啟動防護的功能了。現在我在我的IIS伺服器上加入`Content-Security-Policy default-src 'self'`，上面有提到，一旦啟動CSP後，預設是無法執行html內的JS的，所以今天一樣的輸入便不會觸發JS的執行：

![](/assets/img/Content-Security-Policy_3.png)

IIS HTTP回應標頭設定

![](/assets/img/Content-Security-Policy_4.png)

在開發者工具中，會提到有一段inline script被阻擋了，也會給予開發者CSP的政策設計建議幫助開發人員開啟必要的功能。未來有機會再補上其他政策的實作範例。

## 參考來源

1. [Content-Security-Policy - HTTP Headers 的資安議題 (2)](https://devco.re/blog/2014/04/08/security-issues-of-http-headers-2-content-security-policy/)
2. [XSS 防禦 - CSP script-src 設定](https://blog.darkthread.net/blog/csp-script-src/)
3. [Content Security Policy (CSP)Quick Reference Guide](https://content-security-policy.com/)
4. [CSP script-src unsafe-inline](https://rainmakerho.github.io/2021/06/16/CSP-script-src-unsafe-inline/)
5. [What’s the purpose of the HTML “nonce” attribute for script and style elements?](https://blog.p2hp.com/archives/10701)
6. [CSP source values](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/Sources)
7. [https://github.com/bgres/xss-demo](https://github.com/bgres/xss-demo)