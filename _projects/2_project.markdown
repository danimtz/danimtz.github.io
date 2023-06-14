---
layout: page
title: Shadertoy shaders
description: Some shadertoy shaders including simple raymarching, path tracing...
img: /assets/img/cornell.png
importance: 2
category: personal
---
The following are some highlighted shaders I coded in shadertoy that have helped me learn and practise graphics programming. These are quite old but I thought I would put them here since I was proud of them when I made them at the time.

<hr>
<div class="projects">
  {% assign sorted_shaders = site.shaders | sort: "importance" %}
    <!-- Generate cards for each project -->
    <div class="grid">
    {% for shader in sorted_shaders %}
        {% include shaders.html %}
    {% endfor %}
    </div>

</div>

