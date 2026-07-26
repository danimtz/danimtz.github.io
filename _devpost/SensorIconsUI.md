---
layout: distill

title: Sensor Icons and UI
description: In world sensor icons linked to live readout widgets for the selected room.
img: /assets/img/SensorsUIDevpost/SensorsUIPreview.mp4
importance: 4
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/SensorsUI.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    Entering a room brings up an icon for each of its sensors, anchored in the world at the point being measured, alongside a panel of readout widgets showing the current values for that room.
</div>

Each sensor in the room is marked by an icon pinned to its position in the world, carrying a pictogram for the thing it measures, so presence, power, water flow and the controller units are all distinguishable at a glance rather than needing a legend. The icons stay facing the camera and hold their anchor points as the view orbits the room, which keeps them legible from any angle while still reading as part of the 3D space rather than a flat overlay. Alongside them, a panel of readout widgets gives the actual values for the room, with each measurement getting a presentation suited to it: `Temperatura` and `Humedad` as dial gauges, `Presión Agua` on a coloured range, and the `Caudalímetro` and current draw as direct numeric readouts. For the presence and window sensors, a corresponding 3D hologram of said measurement which can be seen in gray in the video lights up red to indicate an open window or people in the room.


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
