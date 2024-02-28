---
layout: page
title: Density UE5
description: Recreating Destiny 2 in UE5 using Gameplay Ability System and other features.
img: /assets/img/Density.png
importance: 1
category: unreal engine
---

Destiny 2 is one of my favourite videogames, so as a learning exercise I have been trying to recreate the base gameplay of the game as close as possible in Unreal Engine 5. Doing this has allowed me to get experience and learn about the different functionality 
and best practises of in many different features of Unreal Engine 5; from creating dynamic materials for UI components, to importing assets and creating custom animations with sequencer, to using both C++ and Blueprints to tackle the Gameplay Ability System and implement networked multiplayer gameplay.


<div class="youtubevideo">
        <iframe width="640" height="360" src="https://www.youtube.com/embed/UCIADd_qOoE?si=TLDbV2c0TTVYUAyu" frameborder="0" allowfullscreen></iframe>
</div>
<div class="caption">
    Crown of Sorrow encounter multiplayer demo (work in progress)
</div>


<div class="youtubevideo">
        <iframe width="640" height="360" src="https://www.youtube.com/embed/nR0ksjA4WPQ?si=2-Gc946cIlxyZU1t" frameborder="0" allowfullscreen></iframe>
</div>
<div class="caption">
    Density weapons and combat demo
</div>

This is still a work in progress. The main goal apart from practising with UE5 is to recreate the Crown of Sorrow boss encounter, a level from Destiny 2 that was removed from the game in 2020  and I would like to play again.
Below are some posts covering different areas of the project and the workflows used to create them.




<hr>
<div class="devpost">
  {% assign sorted_devpost = site.devpost | sort: "importance" %}
    <!-- Generate cards for each project -->
    <div class="grid">
    {% for devpost in sorted_devpost %}
        {% include devpost.html %}
    {% endfor %}
    </div>

</div>

