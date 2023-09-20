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

<div class="l-body">
        {% include video.html path="assets/video/HealthBar.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Health bar final result video
</div>

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
- If `HealthPercent` < 1, then the material colour must be red with a flashing effect for the `Health` section and a darker semi-transparent flashing red should also occupy the rest of the material.


Since the material is to be used in the UI, first the `Material Domain` was set to `User Interface` in the details tab of the material. Then the material was created as follows:

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/HealthBarMaterial.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar material
</div>

Next, to create the small border that goes around the main health bar, a material using the SDF box function from the [UI Material Lab](https://www.unrealengine.com/marketplace/en-US/product/ui-material-lab) project from Epic Games.

<div class="l-body-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/BorderMaterial.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar border material
</div>

### Creating the UMG health bar widget

A User Widget blueprint was created as seen below. Each component was placed inside a grid panel with the main two being images for the border and health bar. Then an extra horizontal box with images was placed to match the decoration ornaments around the Destiny 2 health bar.

<div class="l-body-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/HealthWidget.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar widget layout
</div>

For the health bar widget image, a dynamic material instance is created on the widgets pre-construct event, and then it is immediatly asigned to it. A widget animation was created for the health bar fadeout and then the functionality was created using four similar functions seen in the image below. The `FadeOutHealthBar` function gets called from C++ when a delegate that monitors the status of the health and shield attributes of the player is fired when they are full and plays the fade out widget animation. The functions which set the value of the `ShieldPercent` and `HealthPercent` parameters in the dynamic material get called from C++ when an `OnGameplayAttributeValueChanged()` delegate for the respective attributes is fired. These functions also call the `ShowHealthBar` function which instantly shows the health bar on the players HUD (doing the opposite of the FadeOutHealthBar function but faster)

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/Functions.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Health bar widget functions
</div>

## Health recovery system

The health recovery system as well as most of the gameplay logic of this project uses Unreal's Gameplay Ability System. (A very good starting resource can be found in [Tranek's GAS documentation](https://github.com/tranek/GASDocumentation)). 

First, the recovery system becomes functional thanks to the `GA_RecentlyDamaged` and `GA_OnShieldBroken` Gameplay Abilities `GA` (These are triggered from events that are handled by the [Damage Pipeline](/devpost/DamagePipeline/)). Their main functionality is to apply a Gameplay Effect `GE` that applies the `State.Combat.RecentlyDamaged` and `State.Combat.ShieldBroken` gameplay tags to the player damaged respectively. 

The `State.Combat.ShieldBroken` is applied by an _infinite_ `GE` that will later be removed within the `GA_Recovery` gameplay ability.

The `State.Combat.RecentlyDamaged` is applied by a  _duration_ `GE`, which determines its duration using the player's `Recovery` attribute and a curve table as follows: 

<div class="l-body">
  {% include figure.html path="assets/img/HealthBarDevpost/RecovCurveTable.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Recovery curve table
</div>

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/GETagApply.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Gameplay tag application gameplay effects
</div>


Once the `State.Combat.RecentlyDamaged` is applied to a player, the `GA_Recovery` ability is automatically activated and handles the rest of the recovery system.

### GA_Recovery Gameplay Ability

The `GA_Recovery` ability is set up to activate when the `State.Combat.RecentlyDamaged` is added to the player's Ability System Component (`ASC`). Once the ability is activated it starts up an ability task which will wait for the tag to be removed (which will happen a few seconds after the player stops taking damage depending on the `Recovery` attribute). 


<div class="l-body-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/RecoveryGA.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GA_Recovery ability activation and end events
</div>

If at any point the player receives damage and the tag is newly added, the ability will cancel, cancelling the main timer handle and will start from the beggining (wait for the tag to be removed ...etc).

The main recovery tick event gets executed once every 16ms from the `Set Timer by Event` node set to looping. On every execution, the health recovery _instant_ `GE` is applied to the player, which increments the player's `Health` attribute by a small amount depending on `Recovery` attribute. This small amount is obtained from the curve table shown previously. On the first execution of the `RecoveryTick` event, if the `State.Combat.ShieldBroken` tag is present, a timer (whose duration again is extracted from the recovery curve table) will be started after which the `GE` that added that tag will be removed from the player and thus removing that tag. After that tag is removed, every further execution of the event will also apply an  _instant_ `GE` which increments the player's `Shield` attribute. 

<div class="l-page-outset">
  {% include figure.html path="assets/img/HealthBarDevpost/RecoveryTick.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GA_Recovery RecoveryTick event logic
</div>

At the end of each execution, the current value of the `Health` and `Shield` attributes are compared to the `MaxHealth` and `MaxShield` attributes to determine if the health bar is completely full, at which point the gameplay ability will end.

## Improvements to be made

While the main functionality of the health bar is finished, a few improvements to the damage taking system will still be made. Currently, a Gameplay Cue `GC` is executed every time a player takes damage to perform a small screen shake, however this feedback will be eventually improved to add the following:

- A blood/red effect around the screen when the player takes damage handled by.
- Different first person flinch animations on the players first person model.
- Niagara particle effect representing the shield being broken around the player when the player's shield is broken.
- Add audio cues to these effects.




[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/projects/density/)         