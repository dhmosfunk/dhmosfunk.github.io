---
title: "Reasearch"
layout: default
permalink: /research/
---


<h1>Reasearch</h1>

<div class="inside-wrapper">
    {% for research in site.research %}
        <div class="post">
            <div class="rText">Title: <span class="rLabel">{{ research.title }}</span></div><br><br>
            <img class="rImage" src="{{ research.image }}" alt="Research Image">
            <div class="rText">Author: <span class="rLabel">{{ research.author }}</span></div>
            <div class="rText">Release Date: <span class="rLabel">{{ research.date }}</span></div>
            <a href="{{ research.link }}">READ</a>
        </div>
    {% endfor %}
</div>