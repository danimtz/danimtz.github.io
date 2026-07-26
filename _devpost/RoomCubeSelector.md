---
layout: distill

title: Room cube selector and creation tool
description: Scriptable tool created to streamline creation 3D room selection cubes
img: /assets/img/RoomToolDevpost/RoomToolPreview.mp4
importance: 2
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/RoomToolExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    The RoomSetupTool running in the editor. After assigning the hotel actor and a room data asset, the corners of the room are picked directly in the viewport, previewed as a box with custom seethrough material, and then committed as the final selection cube.
</div>

Every room in the hotel needs a selection volume so that it can be clicked and inspected in the digital twin. Placing and sizing those cubes by hand across an entire hotel is slow and easy to get wrong, so I built `RoomSetupTool`, a custom editor tool that lives in Unreal's Scriptable Tools Mode. The tool takes the hotel actor and the `GTHRoomInfo` data asset for the room being set up, then walks through the three stages shown in the video: `Select Corner Locations` lets the corners be clicked straight in the viewport, `Preview Room Cube` draws the resulting bounds as a box with a custom material so they can be checked before committing, and `Confirm Cube Creation` finalises the volume. Each button stays disabled until the previous step has been completed, which keeps the workflow in order and makes it obvious what the tool is waiting for. Building it as an editor tool rather than a manual process turned room setup into a repeatable operation that takes a few clicks per room.



[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
