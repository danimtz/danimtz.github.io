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

One of the main improvements that Destiny 2 made over the original game was adding a mantling system. This system works by automatically climbing on to ledges and platforms, greatly improving the game feel when trying to do platforming puzzles (a main section of some levels in the game). Many games have a dedicated button to mantle onto ledges, however Destiny 2 uses a simpler method of activation for the mantling system. The criteria to activate the mantling system are the following: 

- Player must be airborne (not on ground).
- Forward movement input must be pressed.
- Mantle cannot be activated right after a jump (~0.5 seconds must have passed since the player pressed jump to be able to mantle) 
- There must be enough space for the player character on the on top of the ledge.
- Player character must be a set distance away vertically from the edge of the ledge. (E.g: Mantle shouldnt happen if the players eyeline is below the edge to mantle to)
- If a tall mantle is needed, the Player must be looking toward the ledge to mantle to(with some degree of tolerance).

Once a mantle is activated, depending on the vertical distance to the ledge, the player will perform either a short hop animation or a proper mantling animation in which the players left hand ungrips the weapon and moves to simulate placing their hand on the ledge.


## Mantling system

In my project, the mantling system is mainly done in blueprints. On the player characters `Event BeginPlay`, the `MantleCheck` Event is bound to the `On Component Hit` delegate of the players capsule component:

<div class="l-body">
  {% include figure.html path="assets/img/MantleDevpost/InitMantle.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Weapon Init function
</div>


Once a collision is detected, the mantle check event first checks if the other actor in the collision is a `Static Mesh Actor` and then performs condition checks to see if the requirements for mantling are met. The main three checks done at this stage prevent further mantle operations such as line trace and collision checks from being carried out on a collision with a static actor in which the player isnt allowed to mantle in the first place.


<div class="l-body">
  {% include figure.html path="assets/img/MantleDevpost/MantleCheckStart.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Mantle Check function start
</div>

The IsMantling bool prevents a mantle from starting if the player is currently mantling. The CanAttemptMantle function is a [custom function in C++](https://github.com/danimtz/DensityUE5/blob/master/Source/Density/Characters/PlayerCharacters/DensCharacterMovementComponent.cpp#L166)  that comes from the custom CharacterMovementComponent which returns true if the player is airborne and currently pressing the move forward input key. Finally `RecentlyJumped` is a bool that is false for the first 0.5 seconds after a player jumps. Since the jump key event is only registered on the player client, in order for the `RecentlyJumped` variable to be the same on the server and client, a reliable RPC event is used to set the value of `RecentlyJumped` from the client on the server player character as well:

<div class="l-page">
  {% include figure.html path="assets/img/MantleDevpost/RecentlyJumpedRPC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   RecentlyJumped Server/Client RPC system. `On Grounded Jump Triggered` gets called from C++ when the player presses jump key and is currently on the ground
</div>

### Collision Traces check
Once the initial conditions have passed, the main mantle algorithm performs line traces from the facing the forward direction of the player to check if mantling can be performed. A total of 5 line traces are performed (this number can be changed for more or less precision at the cost of performance). To begin, an initial line trace is performed from the top of the capsule forward. If this trace hits something it means that the mantle cannot be performed and the mantle check exits out early without the need to perform more traces:

<div class="l-page">
  {% include figure.html path="assets/img/MantleDevpost/InitialLineTrace.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Performs an initial line trace at the top of the player's capsule. The MantleLineTrace function performs a line trace in the actors forward vector direction, starting at a given ZOffset from the actors origin.
</div>

<div class="l-page">
  {% include figure.html path="assets/img/MantleDevpost/Traces.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Mantle line traces are performed from the top to the bottom of the capsule. Once a line trace returns no hit, the rest of the traces dont need to be performed since an empty space for a posible mantle has been detected and additional checks can be performed from there
</div>

Once once of the line traces for the mantle are confirmed to be valid, we check if the player is facing the correct direction and set the player to be uncrouched to ensure that the following clearance check isnt affected by the player capsule being smaller because of crouching.

<div class="l-page">
  {% include figure.html path="assets/img/MantleDevpost/Crouch.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

Then a clearance check is ran on the expected mantle location. If there is not enough space for the player character, the mantle will not trigger:

<div class="l-page-outset">
  {% include figure.html path="assets/img/MantleDevpost/ClearanceCheck.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
   Clearence check and example using debug setting on the line and capsule trace
</div>

The final step before handling the mantle movement and animations is to set the final mantle position variable and to decide wether a short or a tall mantle animation is needed:

<div class="l-page-outset">
  {% include figure.html path="assets/img/MantleDevpost/FinalPosition.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>


### Movement using motion warping animations

The main movement of the mantle is performed using Unreal Engine 5's motion warping plugin. This plugin allows syncing animation root motion to end up in specific positions and is extremely usful for a task such as this one. For each of the mantle types (short and tall), a third person and first person character animation is needed. The motion warping is driven by the third person animation, as this animation will paly on both the client and the server, while the first person animation is cosmetic and thus client only (since the client is the only one that can see first person animations)



## Improvements to be made

- The mantling animations for the third person character currently only perform the root motion of the character, without any actual animation. A proper animation for the character in third person will be added later.


[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)         