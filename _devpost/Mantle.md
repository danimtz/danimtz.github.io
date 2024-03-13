---
layout: distill

title: Mantle/Ledge grabbing system
description: Creating a mantle system similar to that of Destiny 2 using animation motion warping.
img: /assets/img/devpost_covers/MantleCover.gif
importance: 5
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
# (Under construction, full devblog soon)

## Introduction

This article expains how the mantle system was implemented using a mix of collision tests, traces and UE5's motion warping plugin.

<div class="l-body">
        {% include video.html path="assets/video/MantleExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Final result of mantle system. Shows both short and tall mantle, in first and third person and also demonstrates different edge cases in which the mantle system does not activate such as when theres not enough space to perform a mantle or if the player is not looking toward the mantle surface.
</div>



### Ledge mantle system in Destiny 2



## Mantling system


### Collision check


### Movement using motion warping animations




## Improvements to be made

- The mantling animations for the third person character currently only perform the root motion of the character, without any actual animation. A proper animation for the character in third person will be added later.


[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)         