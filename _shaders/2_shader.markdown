---
layout: page
title: Raymarching SDFs
description: Raymarcher using combinations of signed distance functions to create shapes and shadows
img: /assets/img/burgers.png
importance: 1
category: university
---

Explain how I first implemented the simple raymarcher. Added skylight, sunlight a bounce light and did soft shadow. TODO

<div class="shadertoy">
    <iframe  src="https://www.shadertoy.com/embed/Nls3Rj?gui=true&t=10&paused=true&muted=false" allowfullscreen></iframe>
</div>

After doing that I experimented further by combining the SDF of a torus to cut out a space in the larger sphere to create a fractal-like animation of spheres rotating inside larger spheres.


<!---
<ul id="light-slider">
    <li>
        <h3>First Slide</h3>
        <p>Lorem ipsum Cupidatat quis psdfariatur anim.</p>
    </li>
    <li>
        <h3>Second Slide</h3>
        <p>Lorem ipsum Excepteur amet adipisicing fugiat velit nisi.</p>
    </li>
</ul>


<script type="text/javascript">
    $(document).ready(function() {
    $("ul").lightSlider(); 
    });
</script>--->


<div class="shadertoy">
    <iframe  src="https://www.shadertoy.com/embed/slf3zl?gui=true&t=10&paused=true&muted=false" allowfullscreen></iframe>
</div>
