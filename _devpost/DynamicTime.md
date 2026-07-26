---
layout: distill

title: Dynamic time
description: Driving a dynamic time-of-day system for the hotel environment.
img: /assets/img/DynamicTimeDevpost/DynamicTimePreview.mp4
importance: 5
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/DynamicTime.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    Picking a date from the calendar and scrubbing the time slider. The sun position and interior lighting follow the selected time, and the availability, occupancy and sensor readings shown throughout the application update to match that point in time.
</div>

The digital twin is driven by a selected date and time rather than by the current clock, it defaults to the present, but it can go back in time to check values and events. A calendar lets a specific day be picked, with a `Hoy(Presente)` shortcut for returning to the present, and a slider along the bottom scrubs through the hours of that day. Moving through time drives the environment directly: the sun moves across the sky, and once it drops the building switches over to its interior and exterior artificial lighting, with the windows lighting up room by room. More importantly the data follows the same timeline, so the per floor availability counts, the room occupancy list and the individual sensor readouts all resolve to whatever was recorded for the selected moment. Scrubbing from an afternoon to an evening on the same day shows rooms flipping between `Disponible` and `Ocupada` and the current draw and flow readings changing with them, which is what makes the time control an analysis tool for spotting patterns across a day rather than just a visual time of day setting.



[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
