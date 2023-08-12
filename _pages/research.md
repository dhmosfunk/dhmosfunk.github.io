---
title: "Reasearch"
layout: default
permalink: /research/
---


<h1>Reasearch</h1>

<div class="inside-wrapper">
    {% for research in site.research %}
        <div class="post">
            {{research.title}} <br>
            <img class="rImage" src="{{research.image}}">
            Author: {{research.author}} <br>
            Release Date: {{research.date}} <br>
            <a hrer="{{research.link}}">READ</a> <br>
        </div>
    {% endfor %}
</div>