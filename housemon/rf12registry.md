---
layout: housemon

title: RF12Registry by TheDistractor
subtitle: TODO- Information about HouseMon RF12Registry here......
---

{% raw %}

# RF12Registry 
  
<br/>

##Background
Originally, I just needed a way to write data to one Jeenode running [RF12demo.9] using node.js. I also only ever needed to write to nodeids' that were on the same 'home' band/group as my listening jeenode.  
This was simple, I just provided my own rf12demo-serial Briq with a way to pick up write requests (I chose to add a small unix domain socket listener inside my version of rf12demo-serial).

So....

Time moves on, and my network of nodes expanded beyond what I ever thought. What I do with those nodes has also expanded far beyond simple reporting of roomnodes etc. I now need to take input and output from and to lots of devices, (not just jeenodes). I also needed to simplify (if you could call it that) my rf12demo-serial variant(s). 

Enter the **RF12Registry**.

info....

{% endraw %}