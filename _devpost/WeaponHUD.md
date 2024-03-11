---
layout: distill

title: Weapon HUD, swapping and ammo 
description: Recreating the weapon HUD, ammo system and weapon swap functionality
img: /assets/img/devpost_covers/WeaponSwapCover.gif
importance: 4
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

This article expains how the weapon HUD carousel and ammo system works

<div class="l-body">
        {% include video.html path="assets/video/WeaponHUDDemo.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Example of the weapon HUD showing current ammo reserves, weapon swapping and ammo pickups
</div>



### Weapon HUD and ammo system in Destiny 2

In Destiny 2 a player always has 3 weapons equipped. Theres also 3 different types of ammunition types:

- `Primary` ammo weapons do less damage and have infinite ammo
- `Special` ammo weapons do more damage and have limited reserves
- `Heavy` ammo weapons do considerably more damage have very limited reserves and only one of these weapons can be equipped at a time.

To replenish `Special` and `Heavy` ammo, Destiny 2 uses a weapon ammo pickup system in which enemies sometimes drop ammo crates when killed, green ammo crates replenish `Special` ammo and purple ammo crates replenish `Heavy` ammo.

When it comes to the weapon HUD, the currently equipped weapon is always shown on top, while the unequipped weapons are shown minimized below. The order of the unequipped weapons will always try to respect the originally equipped order, and when swapping weapons it will reorder the unequipped weapons accordingly.

When picking up ammo, a small animation plays as well as showing the amount of newly picked ammo on the side of the UI, and the total reserves number is automatically updated. Finally, when a weapon runs out of ammo, the slot for that weapon turns red and the ammo number starts flashing red.

<div class="l-body">
        {% include video.html path="assets/video/HUD_Destiny2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Example video of the weapon HUD and swapping inside of Destiny 2
</div>

## Ammo system
The player character has a GAS attribute set that holds info for the current equipped weapon. When a weapon is swapped, the data in the attribute set is saved onto a normal set of variables stored in the Player State (so that ammo counts persist even if a player is killed) and the newly equipped weapons saved attributes are loaded onto the GAS attribute set. This is handled by the [WeaponEquipmentComponent in C++](https://github.com/danimtz/DensityUE5/blob/master/Source/Density/Characters/PlayerCharacters/DensWeaponEquipmentComponent.h). The ammo type each weapon uses can be set up in the weapons [WeaponInstance class](https://github.com/danimtz/DensityUE5/blob/master/Source/Density/Weapons/DensWeaponInstance.h) which is a class that holds all the information about how a weapon functions, and is meant to be extended by blueprints for designers to tweak.

When firing a weapon, the firing cost Gameplay Effect removes 1 ammo from the player's currently equipped weapon mag ammo attribute. When the magazine runs out of ammo, the `GA_Reload` ability's `CanActivateAbility` function is overloaded to check wether the weapon has enough ammo to be reloaded. For `Primary` ammo weapons, the reserves are set to -1 to indicate they have infinite ammo:
<div class="l-body">
  {% include figure.html path="assets/img/WeaponHUDDevpost/CanActivateAbilityReload.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Overriden CanActivateAbility function
</div>


The ammo bricks are actors that inherit from custom C++ classes, [DensPickupItem and DensAmmoBrick](https://github.com/danimtz/DensityUE5/tree/master/Source/Density/Items). When a player collides with the ammo brick collision sphere, the brick will check wether the colliding player's corresponding ammo type is full or not. If that ammo type is full, the brick will not be picked up.

<div class="l-body">
  {% include figure.html path="assets/img/WeaponHUDDevpost/AmmoBricks.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Special(green) and Heavy(purple) ammo bricks and collisions with a UE5 mannequin for scale
</div>


## Weapon HUD
The weapon section in the HUD is comprised of a `Weapon Carousel` Widget which holds three different individual `WeaponInfo` Widgets

### Weapon Info Widget


### Weapon Carousel






## Improvements to be made




[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)         