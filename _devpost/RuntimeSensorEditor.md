---
layout: distill

title: Runtime Sensor Editor
description: An in app editor for placing, moving and toggling room sensors while the digital twin is running.
img: /assets/img/SensorEditorDevpost/SensorEditorPreview.mp4
importance: 6
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/SensorEditor.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    The sensor editor running inside the packaged application. Sensors can be spawned, dragged around the room with a 3D gizmo and toggled on or off, with the "Sensores disponibles" list staying in sync with whatever is in the room.
</div>

Sensor layouts are not something you can finalise up front, since where a temperature or humidity probe actually ends up in a room is decided on site rather than in the editor. To avoid a round trip back to Unreal every time a layout changed, sensors can be edited at runtime from inside the running application. Opening the editor for a room brings up the `Sensores disponibles` list, showing each sensor's ID, its type, its position and whether it is currently active, alongside billboard icons in the 3D view that mark where each one sits and what it measures. Selecting a sensor attaches a translate gizmo to it so it can be dragged to a new spot in the room, and the position column in the list updates as it moves. Sensors can also be added, deleted from the list, or flagged as inactive without being removed. Edits are staged rather than applied immediately, so a session can either be committed with `Confirmar Cambios` or thrown away with `Cancelar`.



[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
