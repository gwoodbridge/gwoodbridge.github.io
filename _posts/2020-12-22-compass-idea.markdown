---
layout: post
title: "New Project: Jack Sparrow's Compass"
author: Garrett Woodbridge
date: "2019-12-22 14:14:40 -0600"
tags: "compass"
---

### The Idea
Over the Christmas break, I re-watched the <i>Pirates of the Caribbean</i> films. I thought about how I could recreate the compass Jack Sparrow uses throughout the series. For those who don't know, Jack Sparrow's compass doesn't point North. It points to what you want most. You're probably thinking, Garrett, how are you going to make a compass that knows what you want most. Well, I'm not. But I am going to make a compass that doesn't point North.

The general idea is to use an Arduino Pro Mini connected to a GPS and compass unit. I could then preprogram some destinations that I want to go to. The GPS would be used to get the current location when it powers up. Once I know the current latitude and longitude, I can use a formula to calculate the heading between the current location and the preprogramed destinations. Then, I can use the compass to calculate North, calculate the difference in degrees, and send that to a servo that rotates the compass needle. So not 100% like the compass in the films, but it's still a fun project where I'll get to practice my programming and 3D modeling.
