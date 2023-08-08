---
layout: post
title:  "OpenAI in Unity"
date:   2023-08-07 04:15:07 -0800
categories: jekyll update
featured_image:
---
With the increase in popularity in AI and the amazing things it can do, I decided to explore potential ways that it can be used in games and or XR expereiences. 

I looked into ChatGPT one of the more popular language models and tried to find ways that I can use their API to potentially create something cool.

Because of the way that the OpenAI C# API runs with NuGet, integrating it into Unity wasn't very straightforward. Luckly, there is already a Unity package and Youtube playlist to help me get right into it: [OpenAI in Unity](https://www.youtube.com/playlist?list=PLrE-FZIEEls1-c7QifZYzeq50Id08FcJo)

## ChatGPT, Whisper and DallE

With access to the OpenAI API, I gained access to all three of these models. I wanted to find a clever way to potentially use all 3 of these in a single experience. Whisper, being a transctiption and translation tool, was easy to use with either DallE or ChatGPT.

The experience I ended up deciding on was one that allows the user to speak a small description of a texture and has DallE generate an image which is then skinned onto an object.

## Demo Videos

I have three demo videos in this playlist in the order that I implemented features:

[Demo Videos](https://www.youtube.com/playlist?list=PLzFymnp51SV-kBJ1uIoxA8MP92sXtNwRu)

The first video is converting the image into a material and using that to skin the space ship.

The second video is the VR implementation and making the UI into world space so it can be used in VR space.

The 3rd video is the working Whisper implementation that lets the user speak a description.

## Some Issues

If you do end up working with the OpenAI API and Unity, be extra careful to not put any API calls in the update function, I made a mistake and my program called the API 50 times before it got cut off for too many requests. Thankfully there was even a cut off so I was only charged $1 for my mistake.

