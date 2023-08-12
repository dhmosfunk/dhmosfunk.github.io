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
            {{research.link}} <br>
            {{research.author}} <br>
            {{research.date}}
        </div>
    {% endfor %}
</div>