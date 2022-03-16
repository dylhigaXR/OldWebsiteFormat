---
layout: post
title:  "Hand Grabbing: #1"
date:   2022-03-10 04:15:07 -0800
categories: jekyll update
featured_image:
---
This time around I decided I wanted to look closer into what the oculus integration had to offer.
There was no implementation yet but I found this handy guide that gave me a rough idea about how to start.


<iframe width="560" height="315" src="https://www.youtube.com/embed/UoSzhoZ18bE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Cleaning up the grabbing

While the video gave me a good place to start, there were some weird things going on while I was grabbing the objects.
I came across a bug where when the pinch strength for my hand was too weak it would release the item and I would be unable to pick it up again. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/g7AYl2puUsg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Looking at this code snippet from the video I figured the bug had to do with something in the if statement here:

{% highlight ruby %}
if (!m_grabbedObj && isPinching && m_grabCandidates.Count > 0) 
{
    GrabBegin();
} else if(m_grabbedObj && !isPinching)
{
    GrabEnd();
}
{% endhighlight %}

Looking through the OVRGrabber script I realized that when GrabEnd() is called the list of grabbable objects, m_grabCandidates, is cleared through GrabVolumeEnable().

{% highlight ruby %}
protected virtual void GrabVolumeEnable(bool enabled)
    {

        ***

        if (!m_grabVolumeEnabled)
        {
            m_grabCandidates.Clear();
        }
    }
{% endhighlight %}

This caused a problem due to m_grabCandidates.Count needing to be over zero but only being updated when the trigger is entered or exited. 
However, when the pinch strength gets too weak and GrabEnd() is called, the colliders are still overlapping meaning the value will be zero unless the player moves their hand far enough away to un-overlap the colliders.

![RayCastDemo]({{ "../assets/Handbox.PNG" | relative_url }})

I first thought about checking for objects every frame where there is a trigger overlap with OnTriggerStay. 
However, I came to the conclusion of omitting the dictionary completely and it ended up working fine. 

This also increased the smoothness of grabbing objects because if the pinch strength was lost but regained immediately, it looks as if the object was never released.

I can see why this wouldn't have been a problem with the controllers and grabbing because weather or not the grab button is pressed is a matter of true or false.
However, with pinch strength, even while the player is "grabbing", the grab could be released for a split second causing this type of unintended interaction.

<iframe width="560" height="315" src="https://www.youtube.com/embed/CTuBmr2FFRs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Thanks for reading!