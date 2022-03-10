---
layout: post
title:  "Hand Grabbing: #1"
date:   2022-03-10 04:15:07 -0800
categories: jekyll update
---
This time around I decided I wanted to look closer into what the oculus integration had to offer.
There was no implementation yet but I found this handy guide that gave me a rough idea about how to start.


<iframe width="560" height="315" src="https://www.youtube.com/embed/UoSzhoZ18bE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Cleaning up the grabbing

I came across a bug where when the pinch strength for my hand was too weak it would release the item and I would be unable to pick it up again. 
Looking through OVRGrabber I realized that when GrabBegin is called the list of grabbable objects is cleared. 
This caused a problem due to m_grabCandidates only being updated when the trigger is entered or exited. 
I first thought about checking for objects every frame where there is a trigger overlap with OnTriggerStay. 
However, I came to the conclusion of omitting the dictionary completely and it ended up working fine. 
This also increased the smoothness of grabbing objects because if the pinch strength was lost but regained immediately, it looks as if the object was never released.