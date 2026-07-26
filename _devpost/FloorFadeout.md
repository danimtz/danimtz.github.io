---
layout: distill

title: Floor fadeout
description: Dynamically fade out all meshes above the selected level to reveal the hotel interior.
img: /assets/img/FadeFloorDevpost/FadeOutFloorsPreview.mp4
importance: 1
category: gemturh

tags: distill formatting
giscus_comments: true
date: 2026-01-01
featured: true

toc: true
---

## Introduction

<div class="l-body">
        {% include video.html path="assets/video/FadeOutFloorsExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true muted=true %}
</div>
<div class="caption">
    Floor fadeout in action. Selecting a floor from the "Estado de plantas" panel fades out every level above it and moves the camera to a top down view of the chosen floor, revealing the room layout underneath.
</div>

The floor selector in the digital twin allows the user to go to a floor and view it as a cross section of the building, it does this by fading out every mesh sitting above the level the user picks. The "Estado de plantas" panel lists each floor alongside its room availability, and choosing one transitions the camera to a top down view of that floor while the levels above it dissolve away. Because the floors above are faded rather than deleted, the outer shell and surrounding terrain stay visible for context, and the transition reads as a continuous movement into the building instead of a hard cut. The same mechanism drives every floor in the hotel, including the roof when the topmost level is selected.

The actual fadeout works using a `Material Parameter Collection` value called `floor height` against the objects `absolute world position` to fade out all meshes above a threshold using the material's `opacity mask`. The value is smoothly changed from one floor height to another using a timeline inside the hotel's main blueprint actor. Then when the full floor has been faded away all meshes belonging to that floor get deactivated in the editor via a `Data Layer` to avoid collisions with other systems and general optimization.




[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/gemturh/)
