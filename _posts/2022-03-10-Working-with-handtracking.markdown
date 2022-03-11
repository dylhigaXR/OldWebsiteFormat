---
layout: post
title:  "Fireball simulator"
date:   2022-03-10 04:15:07 -0800
categories: jekyll update
---
My first project on the magic leap was working with hand tracking to create a small fire ball spell casting simulator. 

## Creating the Fire Ball

The key difference between these two development experiences is the fact that I had access to gesture recognition with the Magic Leap SDK, but nothing with the Oculus Integration from unity.
I figured raycasts were the best way to go about this.

By using the transform of the center of the players hands provided with lHandCenter.transform.position and rHandCenter.transform.position:

{% highlight ruby %}
RaycastHit frontHitl;
RaycastHit frontHitr;

 if (Physics.Raycast(lHandCenter.transform.position, lHandCenter.transform.TransformDirection(new Vector3(-0.15f, 1.0f, 0.0f)), out frontHitl, Mathf.Infinity, layerMask))
       if (Physics.Raycast(rHandCenter.transform.position, rHandCenter.transform.TransformDirection(new Vector3(0.15f, -1.0f, 0.0f)), out frontHitr, Mathf.Infinity, layerMask))
          {
                fireBallClone = Instantiate(FireBall);
                handsFacing = true;
          }
 }
{% endhighlight %}

This leads to:
![RayCastDemo]({{ "../assets/RayCastDemo.PNG" | relative_url }})

Then, by using the middle point formula:

{% highlight ruby %}
Vector3 middlePos = lHandCenter.transform.position + (rHandCenter.transform.position - lHandCenter.transform.position)/2; 
{% endhighlight %}

The fire ball instantiates and stays between the palms.

![FireBallDemo]({{ "../assets/FireBallDemo.PNG" | relative_url }})

By using layer masks the ray casts will only collide with the other hands collider.

## Shooting the Fire Ball

Now comes the problem of detecting when the gesture of shooting occurs.
The best way I could think about doing this was to invert the direction of the raycast and have them search for a hitbox behind the player.

However, I wanted to make it so the player only had a limited time after moving their hands to shoot the fireball.

{% highlight ruby %}
 RaycastHit backHitl;
 RaycastHit backHitr;

 timer += Time.deltaTime;
 if (timer - startTime < 0.1) {
    if (Physics.Raycast(lHandCenter.transform.position, lHandCenter.transform.TransformDirection(new Vector3(0.15f, -1.0f, 0.0f)), out backHitl, Mathf.Infinity, layerMaskHead))
        {
           if (Physics.Raycast(rHandCenter.transform.position, rHandCenter.transform.TransformDirection(new Vector3(-0.15f, 1.0f, 0.0f)), out backHitr, Mathf.Infinity, layerMaskHead))
              {
                fire = true;
                instantiated = false;
              }
        }
 }
{% endhighlight %}

This is a makeshift timer system that roughly tracks if 1/4 of a second has passed.
The ray casts will be shot out from the back of the hands during this time and if nothing is found then the ball will be destroyed.
However, if the ray casts do hit the player, the ball will be shot "forward".

In order to not complicate the travel direction I made it so the fire ball would be facing the player and then would be shot backwards to simulate it being shot forward... oddly enough.

{% highlight ruby %}
 ballClone.transform.LookAt(new Vector3(head.transform.position.x, ballClone.transform.position.y, head.transform.position.z));
 ballClone.transform.position += -ballClone.transform.forward * Time.deltaTime * 5;
{% endhighlight %}

This is the core of my design for this:

<iframe width="560" height="315" src="https://www.youtube.com/embed/dYvZZ-Q9H5k" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Thanks for reading!
