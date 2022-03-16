---
layout: post
title:  "Fireball Simulator"
date:   2022-03-10 04:15:07 -0800
categories: jekyll update
featured_image:
---
When the Magic Leap 1 first came out the first thing I tried to do was build an application with their hand tracking and gesture recognition features. This ended up becoming a fireball simulation where
the fireball would be shot like you were a wizard. I recently got an Oculus Quest 2 and wanted to see if I could recreate that project in VR.

## Creating the Fireball

The key difference between these two development experiences is the fact that I had access to gesture recognition with the Magic Leap SDK, but nothing with the Oculus Integration from Unity.
I figured that using ray casts were the best way to make up for the lack of gesture recoginitions.

By using the transform of the center of the players hands provided with lHandCenter.transform.position and rHandCenter.transform.position I could shoot ray casts that would tell me when the hands were facing each other.

{% highlight ruby %}
RaycastHit frontHitl;
RaycastHit frontHitr;

 if (Physics.Raycast(lHandCenter.transform.position, 
                     lHandCenter.transform.TransformDirection(new Vector3(-0.15f, 1.0f, 0.0f)), 
                     out frontHitl, Mathf.Infinity, layerMask))
     if (Physics.Raycast(rHandCenter.transform.position, 
                         rHandCenter.transform.TransformDirection(new Vector3(0.15f, -1.0f, 0.0f)), 
                         out frontHitr, Mathf.Infinity, layerMask))
         {
             fireBallClone = Instantiate(FireBall);
             handsFacing = true;
         }
 }
{% endhighlight %}

This leads to:
![RayCastDemo]({{ "../assets/RayCastDemo.PNG" | relative_url }})

Then, by calculating the mid point between 2 points in space with mid = A.position + (B.position - A.position)/2:

{% highlight ruby %}
Vector3 middlePos = lHandCenter.transform.position + 
                    (rHandCenter.transform.position - lHandCenter.transform.position)/2; 
{% endhighlight %}

The fireball instantiates and stays between the palms.

![FireBallDemo]({{ "../assets/FireBallDemo.PNG" | relative_url }})

By using layer masks the ray casts will only collide with the other hands collider.

## Shooting the Fireball

Now comes the problem of detecting when the player pushes their hands foward, indicating the fireball will be shot.
The best way I could think about doing this was to invert the direction of the ray cast and have them search for a hitbox behind the player.

However, I wanted to make it so the player only had a limited time after moving their hands to shoot the fireball.

{% highlight ruby %}
 RaycastHit backHitl;
 RaycastHit backHitr;

 timer += Time.deltaTime;
 if (timer - startTime < 0.1) {
     if (Physics.Raycast(lHandCenter.transform.position, 
                         lHandCenter.transform.TransformDirection(new Vector3(0.15f, -1.0f, 0.0f)), 
                         out backHitl, Mathf.Infinity, layerMaskHead))
          {
          if (Physics.Raycast(rHandCenter.transform.position, 
                              rHandCenter.transform.TransformDirection(new Vector3(-0.15f, 1.0f, 0.0f)), 
                              out backHitr, Mathf.Infinity, layerMaskHead))
               {
                   fire = true;
               }
          }
 }
{% endhighlight %}

This is a makeshift timer system that roughly tracks if 1/4 of a second has passed.
The ray casts will be shot out from the back of the hands during this time and if nothing is found then the ball will be destroyed.
However, if the ray casts do hit the player, the ball will be shot "forward".

In order to simplify the travel direction I made it so the fireball would always be facing the player by using LookAt(). Then, when shot, the fireball would move in its relative backwards direction to simulate it being shot forward... oddly enough.
If I didn't do this the ball wouldn't rotate while the player moves and the direction of the ball would have to be calculated each time.

{% highlight ruby %}
 ballClone.transform.LookAt(new Vector3(head.transform.position.x, ballClone.transform.position.y, head.transform.position.z));
 ballClone.transform.position += -ballClone.transform.forward * Time.deltaTime * 5;
{% endhighlight %}

This is the core of my design for this:

<iframe width="560" height="315" src="https://www.youtube.com/embed/dYvZZ-Q9H5k" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Thanks for reading!
