---
layout: page
title: GemTurH (Digital Twin)
description: A digital twin in Unreal Engine 5 for visualization and energy efficiency analysis of a hotel building.
img: /assets/img/GemTurThumbnail.png
importance: 1
category: unreal engine
---

While working at Plexus Tech I was the Unreal Engine Developer in charge of the entire UE5 section of GemTurH, a digital twin of a hotel built in Unreal Engine 5. The application allowed users to visualize hotel rooms in real time and carry out energy efficiency analysis of them. 

[Here on this link](https://www.linkedin.com/posts/goodnews-gemturh-nextgenerationeu-ugcPost-7432776466578169857-hZ2j/) you can see the post my company made on LinkedIn about it. The project was setup with multiple configurations such as VR and Unreal Engine's PixelStreaming framework to be able to access it from a web browser while still maintaining advanced visual fidelity (Unfortunately that is not publically available)



<div class="youtubevideo">
        <iframe width="640" height="360" src="https://www.youtube.com/embed/LLRtQA94dOY" frameborder="0" allowfullscreen></iframe>
</div>
<div class="caption">
    GemTurH Hotel Digital twin demo video
</div>


Below are some breakdowns of some of the features seen in the video above explained in more detail:


<hr>
<div class="devpost">
  {% assign sorted_devpost = site.devpost | where: "category", "gemturh" | sort: "importance" %}
    <!-- Generate cards for each gemturh devpost article -->
    <div class="grid">
    {% for devpost in sorted_devpost %}
        {% include devpost.html %}
    {% endfor %}
    </div>

</div>