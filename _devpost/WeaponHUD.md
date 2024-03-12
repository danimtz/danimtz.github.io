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
The weapon info widget gives the player information about the current weapon. It has the following dynamic elements that are initialized when a new weapon is equipped:

<div class="l-body">
  {% include figure.html path="assets/img/WeaponHUDDevpost/WeaponInfo.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

1. Weapon icon
2. Weapon element icon
3. Mag ammo
4. Ammo reserves
5. Ammo type bar (white, green or purple depending on ammo type)
6. Recently picked up ammo (+ sign followed by number of ammo goes there)


The following function is called when initializing a new weapon instance with the UI:
<div class="l-page-outset">
  {% include figure.html path="assets/img/WeaponHUDDevpost/WeaponInit.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Weapon Init function
</div>

The weapon info widget also has widget animations that play when the weapon is equipped/unequipped and when ammo is picked up:

<div class="l-body">
  {% include figure.html path="assets/img/WeaponHUDDevpost/EquipAnim.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
  {% include figure.html path="assets/img/WeaponHUDDevpost/NewAmmoAnim.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>


### Weapon Carousel
The weapon carousel widget holds three separate weapon info widgets. The currently equipped weapon info widget is set at the top, while the unequipped weapons are layed out minimized under it:

<div class="l-body-outset">
  {% include figure.html path="assets/img/WeaponHUDDevpost/WeaponCarousel.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Weapon Carousel base layout
</div>

When the player swaps weapons, an event is called from C++ in the Weapon Carousel widget that moves the selected weapon to the topmost slot, playing the equipped animation(from the weapon info widget above) while simultaneously playing the weapon unequip animation on the previously equipped weapon. The event then runs a short algorithm that decides where each of the unequipped weapons should be placed in the carousel to try and respect the original equip order.

Once the position of each of the widgets is decided, an FInterp To node is used to smoothly move each of the weapon info widgets to their corresponding location. The following function is ran each tick to simulate a widget animation until the widget position is equal to the target position:

<div class="l-page-outset">
  {% include figure.html path="assets/img/WeaponHUDDevpost/MoveToPositionFunction.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Move to position function. Ran each tick until the condition is met. TargetPosition array calculated by weapon ordering algorithm.
</div>

### Weapon Ordering Algorithm




## Improvements to be made




[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)         