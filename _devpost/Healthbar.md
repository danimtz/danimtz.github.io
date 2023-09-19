---
layout: distill

title: Health bar and recovery
description: Recreating the destiny 2 health bar and recovery system using materials, UMG and GAS.
img: /assets/img/devpost_covers/RecovGif.gif
importance: 1
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
In this article I will show and explain how the healthbar system from Destiny 2 was recreated in Unreal Engine to give the following result. 

GIF

The visual effects are all carried out in a single material within the user widget, while the health recovery is managed using gameplay abilities.
### How the healthbar and recovery work in Destiny 2
In Destiny 2, the healthbar UI is hidden until a player takes damage. Once damage occurs, the healthbar immediatly appears and displays the damage dealt, staying up in the HUD until a players health is fully recovered. The actual healthbar is split into two main sections, the player's `Shield` and the player's `Health`.

<div class="l-body">
  {% include figure.html path="assets/img/HealthBarDevpost/D2HealthBar.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Example health bar from Destiny 2
</div>

The `Shield` makes up the first 70% of a players health bar. When the `Shield` is damaged but not fully depleted, the bar's colour remains white. However when the `Shield` is broken (apart from audio, visual effects and other potential gameplay related effects triggering), the healthbar becomes red and flashing (When a player's `Health` is full but the `Shield` is depleted, the flashing effect stops but the color remains red). After a few seconds of not taking damage the player will start regenerating their `Health` and a few seconds after that the `Shield` will start regenerating. Any further damage taken will stop the regeneration and start the timer again. The rate of regeneration and the waiting time since the last damage was taken all depend on the player's `Recovery` stat. The `Recovery` stat goes from 0 to 100, increasing its efficacy every 10 levels. For example a player with 0 recovery will back to full healthbar  in 9 seconds after being damaged while a player with 100 recovery will achieve the same in 6 seconds.



## Health bar UI
### Health bar material

The first step was to create a material that would manage the health bar depleting and regenerating based on two float values, `HealthPercent` and `ShieldPercent` which should be between 0 and 1. To emulate the behaviour explained above the following constraints had to be followed:

- `Health` must occupy the first 30% of the material and the `Shield` the last 70%
- If `ShieldPercent` > 0, then the material colour must be white.
- If `ShieldPercent` = 0 and `HealthPercent` = 1, then the material colour must be red
- If `ShieldPercent` = 0 and `HealthPercent` < 1, then the material colour must be red with a flashing effect for the `Health` section and a darker semi-transparent flashing red should also occupy the rest of the material.


Since the material is to be used in the UI, first the `Material Domain` was set to `User Interface` in the details tab of the material. Then the material was created as follows:

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/HealthBarMaterial.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar material
</div>

Next, to create the small border that goes around the main health bar, a material using the SDF box function from the [UI Material Lab](https://www.unrealengine.com/marketplace/en-US/product/ui-material-lab) project from Epic Games.

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/BorderMaterial.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar border material
</div>

### Creating the UMG health bar widget

## Health recovery system
### Recovery system using gameplay abilities


## Improvements to be made
