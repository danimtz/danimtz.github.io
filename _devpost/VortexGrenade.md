---
layout: distill

title: Vortex Grenade VFX
description: Recreating the destiny 2 vortex grenade using UE5's Niagara Particle System
img: /assets/img/devpost_covers/VortexGrenade.gif
importance: 3
category: density

tags: distill formatting
giscus_comments: true
date: 2021-05-22
featured: true



# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc: true

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---

## Introduction
In this article I will show and explain how the Vortex Grenade visual effects were recreated using Niagara in UE5. 

<div class="l-body">
        {% include video.html path="assets/video/VortexGrenadeExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Vortex Grenade Niagara VFX final results in-game.
</div>

### Vortex Grenade effects in Destiny 2

Here is an example video of the original effect:

<div class="l-body">
        {% include video.html path="assets/video/D2VortexNade.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Vortex Grenade inside of Destiny 2.
</div>


## Niagara particle effects breakdown

The Niagara system ended up looking like this:

<div class="l-page-outset">
  {% include figure.html path="assets/img/VortexDevpost/NiagaraEmmiters.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Final Niagara system
</div>


In order to recreate the effects of the vortex grenade, I divided the effects into three main sections:

- The impact decal which is applied onto the surface the grenade hits and lingers for a few seconds after its duration expires.
- The main explosion effects which are the initial effects shown when the grenade impacts a surface or target.
- The vortex effect which lingers where the grenade impacted for a few seconds (this is the area of effect that applies damage if any targets enter it) before dissapearing.


<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/VortexNadeFinal.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Final result of the Niagara system
</div>


### Impact decal

The impact decal is handled by a single Niagara Emmiter using a _Decal Renderer_ (a new emmiter renderer introduced in UE 5.2). Most of the effects for the decal are handled inside the material. Through the use of noise textures, the cracks and energy effects are randomized each time the decal is spawned by adding a random noise offset to the noise texture's input uv coordinates.

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/HitDecal.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Impact decal
</div>




### Main explosion

For the main explosion, the effects were divided into 5 different sprite emmiters shown below:

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Main_1.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Bright smoke flash
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Main_2.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Darker smoke flash
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Main_3.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Projectile pellets with ribbon and random particle emmiters
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Main_4.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Explosion particules
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Main_5.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Random direction noise spike sprites
</div>






### Vortex

Finally the vortex effects are a mix of sprite and mesh renderer emitters that start playing on the emitter with a second delay with respect to the main explosion emmiters


<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_1.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Converging particles
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_2.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Converging sphere
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_3.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Continuous converging particles
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_4.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Vortex core
</div>

<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_5.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Vortex core overlay
</div>


<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_6.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Spherical ribbons that go around sphere in random directions
</div>


<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_7.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Vortex converging sprite
</div>


<div class="l-body">
  {% include figure.html path="assets/img/VortexDevpost/Vortex_8.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Energy and smoke spherical mesh outline
</div>





### Screen shake

As an extra game-feel addition, when the grenade first spawns the vortex effects, a small screen shake effect is palyed at the location of the grenade to all nearby players. This can be seen in the video at the top of the article (around the 20 second mark).



[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)         