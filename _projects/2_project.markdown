---
layout: page
title: Shader Collection
description: A collection of shadertoy shaders including simple raymarching, path tracing and more...
img: /assets/img/cornell.png
importance: 2
category: personal
---
The following are some highlighted shaders I coded in shadertoy that have helped me learn and practise graphics programming. You can find the full list and code in my <a href="https://github.com/danimtz/" target="_blank">github</a> or <a href="https://www.shadertoy.com/user/Danimtz" target="_blank">shadertoy</a> profiles.

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

