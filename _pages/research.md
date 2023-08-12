---
title: "Reasearch"
layout: default
permalink: /research/
---


<h1>Reasearch</h1>

<div class="inside-wrapper">
    {% for research in site.research %}
        <div class="post">
            <div class="rText"><span class="rLabel">Title:</span> {{ research.title }}</div>
            <img class="rImage" src="{{ research.image }}" alt="Research Image">
            <div class="rText"><span class="rLabel">Author:</span> {{ research.author }}</div>
            <div class="rText"><span class="rLabel">Release Date:</span> {{ research.date }}</div>
            <a href="{{ research.link }}">READ</a>
        </div>
    {% endfor %}
</div>