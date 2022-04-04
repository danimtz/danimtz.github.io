---
layout: page
title: Final year project
description: Template matching using Best Buddies Similarity and its application to cloud tracking
img: /assets/img/FYP_cover.png
importance: 1
category: university
---

My final year project for my degree at University of Bath was an investigative project which involved studying and implementing a novel method of template matching called Best-Buddies Similarity(BBS) and assess its performance for meteorological applications, more specifically cloud tracking when compared to other template matching methods.

The Best Buddies Similarity algorithm showed great performance, accuracy and robustness to noise and deformation when tracking objects, people animals etc in video. This robustness made it a potential option for cloud motion tracking as deformation and noise corruption is commonly found in cloud sattelite images. Two different implementations of the BBS algorithm alongside other template matching methods (Ordinal measures(OM), cross correlation coefficient(CCC), sum of squared differences(SSD/SAD)) were implemented in MATLAB and tested across a range of images taken from EUMETSAT. These images were 'manually' corrupted by impulse and gaussian noise to test the performance of each algorithm against noise. As a final phase of the investigation, each of the template matching methods were used as the input of a relaxation labelling framework, a procedure which involves approximating and corecting motion vectors iteratively based on the best motion vectores of nearby templates.

Overall the BBS algorithm was deemed to be a useful method for cloud tracking, however not enough to de-throne other alternatives in either speed (CCC) or noise performance (OM). BBS provides great robustness and matching performance by being able to distinguish the object(even when deformend) from the background, however for cloud sattelite images, the background of each template does not differ greatly from the actual template.

Read the full <a href="{{ '/assets/pdf/FYPreport.pdf' | relative_url }}" target="_blank">report/project</a>. A summary poster for the project is shown here:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/FYPposter.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>


