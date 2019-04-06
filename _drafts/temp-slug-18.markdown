---
layout: post
title: Deck.gl - Taxi Trip Visualization
image: https://nghiatran.me/wp-content/uploads/2017/04/osman-rana-206191.jpg
---


Hi everyone.

I’ve received a lot of request to continue writing part-2. So why not 🤗

tl;dr

If you don’t have patient, here is result we will get in this tutorial

[]

In next couple mins, we’re trying to manipulate taxi trip on map. The interesting part present taxi segment from origin to destination in realtime.

 

Technologies

 

Prepare data

Google Direction API

Taxi trip NY

Pre-processing dataset

Final dataset

 

Trip Layer

 

Reminder

Starter-kit


## 3. Implement Taxi Layer

### 3.1 Overall

Before going to implement TaxiLayer. It would be better if we know how it looks like in invadually taxi line.

As you can see, the taxi line is a curve. Actually, it’s connection of each line segment. In overall prespective, it looks like the curve.

 

 

GLSL Model

Tween time

First action

Building model

 

 


