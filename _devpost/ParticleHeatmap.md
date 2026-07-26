---
layout: distill

title: Particle heatmap
description: Visualizing real-time temperature data as a GPU particle heatmap.
img: /assets/img/HeatmapDevpost/HeatmapPreview.mp4
importance: 3
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/Heatmap.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    The particle heatmap filling every room with sensors on a floor. The room volumes are populated with a grid of particles coloured by the temperature reading for that room, and the visualisation holds up as the camera orbits the floor.
</div>

The hotel is fitted with sensors throughout, but a table of raw temperature readings is hard to reason about when what you actually want to know is how heat is distributed across a floor. This visualisation renders that data as a particle grid heatmap using `Niagara` grids, filling each room with a regular grid of particles that are coloured by the value being read for that space. The room volume delimitation is carried out by culling particles based on collision with meshes from the rooms. That way if a window is open, the particles will propagate past it as can be seen in one of the rooms in the video above. Because the grid is simulated on the GPU through Niagara, an entire floor of rooms can be displayed at once with a high number of particles while still being performant.




[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
