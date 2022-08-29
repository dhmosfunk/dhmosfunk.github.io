---
layout: single
title: Pandora [boot2root] - Hack The Box
excerpt: " Description "
date: 2022-1-18
classes: wide
header:
  teaser: /assets/images/htb-writeup-pandora/logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - boot2root
tags:  
  - SNMP Enumeration
  - CVE-2021-32099
  - SQL Map
  - SSH Port Forward
  - Upload Files
  - SSH-Keygen
  - Linux-SSH-Key-Perms
  - SUID
  - Path-Hijacking
  - Tar-Hijacking
---

<h1 align="center">
<img src="/assets/images/htb-writeup-pandora/banner.PNG">
</h1>



## Network Enumeration

```
[*]---------------------------------[TCP SCAN]---------------------------------[*]
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 24:c2:95:a5:c3:0b:3f:f3:17:3c:68:d7:af:2b:53:38 (RSA)
|   256 b1:41:77:99:46:9a:6c:5d:d2:98:2f:c0:32:9a:ce:03 (ECDSA)
|_  256 e7:36:43:3b:a9:47:8a:19:01:58:b2:bc:89:f6:51:08 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Play | Landing
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
[*]---------------------------------[TCP SCAN]---------------------------------[*]


[*]---------------------------------[UDP SCAN]---------------------------------[*]
PORT      STATE         SERVICE VERSION                                                                                                    
161/udp   open          snmp    SNMPv1 server; net-snmp SNMPv3 server (public) 
[*]---------------------------------[UDP SCAN]---------------------------------[*]
```


## HTTP Server 

<img src="/assets/images/htb-writeup-pandora/html_server.PNG">

## SNMP Enumeration

<img src="/assets/images/htb-writeup-pandora/snmp_enum_creds.PNG">

## Login to SSH and Port Forward port 80

<img src="/assets/images/htb-writeup-pandora/port_forward_http.PNG">

## Panda web server

<img src="/assets/images/htb-writeup-pandora/panda_web_server.PNG">

## Enumerate for CVE 2021-32099

<img src="/assets/images/htb-writeup-pandora/cve_2021_32099.PNG">

<img src="/assets/images/htb-writeup-pandora/cve_explain.PNG">

## Exploit CVE 2021-32099

<img src="/assets/images/htb-writeup-pandora/sql_injection_path.PNG">

<img src="/assets/images/htb-writeup-pandora/sql_map_exploit.PNG">

<img src="/assets/images/htb-writeup-pandora/admin_cookie.PNG">

<img src="/assets/images/htb-writeup-pandora/change_cookie.PNG">

<img src="/assets/images/htb-writeup-pandora/success_login.PNG">

## Reverse Shell Upload

<img src="/assets/images/htb-writeup-pandora/upload1.PNG">

<img src="/assets/images/htb-writeup-pandora/upload2.PNG">

<img src="/assets/images/htb-writeup-pandora/execute_shell.PNG">

<img src="/assets/images/htb-writeup-pandora/matt_user_reverse.PNG">


## Enumerate for SUID files

<img src="/assets/images/htb-writeup-pandora/search_for_suid.PNG">

<img src="/assets/images/htb-writeup-pandora/suid_file.PNG">

##  Enumerate pandora_backup binary

<img src="/assets/images/htb-writeup-pandora/tar_hijhacking.PNG">

## Login with SSH

<img src="/assets/images/htb-writeup-pandora/generate_ssh_keys.PNG">

<img src="/assets/images/htb-writeup-pandora/ssh_perm.PNG">

<img src="/assets/images/htb-writeup-pandora/http_server.PNG">

<img src="/assets/images/htb-writeup-pandora/login_ssh.PNG">

## Exploit PATH Hijacking 

<img src="/assets/images/htb-writeup-pandora/root_path_hijacking.PNG">

## References 
[Pandora FMS 742: Critical Code Vulnerabilities Explained](https://blog.sonarsource.com/pandora-fms-742-critical-code-vulnerabilities-explained)<br> 

[CVE-2021-32099](https://cve.ics-csirt.io/cve/CVE-2021-32099)<br>

[UNIX Port in use](https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/)<br>

[Set up SSH public key authentication to connect to a remote system](https://kb.iu.edu/d/aews)<br>

[Hijacking Relative Paths in SUID Programs](https://medium.com/r3d-buck3t/hijacking-relative-paths-in-suid-programs-fed804694e6e)
