---
title: "Reasearch"
layout: default
permalink: /research/
---


<h1>Reasearch</h1>

<div class="inside-wrapper">
    {% for research in site.research %}
        <div class="post">
            Tittle: <div class="rText">{{research.title}}</div> <br>
            <img class="rImage" src="{{research.image}}">
            Author: <div class="rText">{{research.author}}</div> <br>
            Release Date: <div class="rText">{{research.date}}</div> <br>
            <a hrer="{{research.link}}">READ</a> <br>
        </div>
    {% endfor %}
</div>