---
title: Animations with d3 and react 
author: Dimitris Georgoulas
date: 2020-12-02 11:30:00 +0200
categories: [Web-Development, Javascript]
tags: [javascript, react, d3]
toc: true
---

## Learning React and its ecosystem
Well, after the unpleasant [experience]({% post_url 2020-06-03-zakanda_technical_overview %}) of creating a 
single page web application without using a front-end framework, learning one, was a top priority. Since I really liked 
the idea of an html template language combined with the full power of javascript (see JSX) I chose React. After watching 
some introductory tutorials, I dived into React's documentation, which is really good by the way, and as any 
full stack web developer that respects himself, I looked at how I could combine react with django. 
Having my homework done, I proceeded to apply and test those skills by building a small project. The idea
was to go beyond the basic CRUD functionality and have a bit more fun creating an animation of some sort. 

In my search for animation ideas, I found Ben Frederickson's [blog](http://www.benfrederickson.com/blog/) 
which contains some interesting animations. Since I wanted something simple for my small demo project, I chose to 
implement the [Heap Sort](http://www.benfrederickson.com/heap-visualization/) algorithm. And voila... 
I named this small project [algozoom](http://algozoom.com/).

 ![Desktop View]({{ "/assets/img/sample/minHeap_forever_3.gif" | relative_url }}) 
 
The animation is made with the amazing d3.js library with which you can bind the DOM with arbitrary data 
and then manipulate it by modifying the data. Apart from that, 
I used Redux to store global state and as I wanted to follow best practices and reduce boilerplate code, I wrote redux logic using the 
[Redux toolkit](https://redux-toolkit.js.org/) with its nice utilities like `createSlice` and `createAsyncThunk`. 
I also used the [Devias Kit Pro](https://material-ui.com/store/items/devias-kit-pro/) for the layout and that was a 
wonderful choice since not only it supports a lot of things out of the box, but most importantly, it is very well written. 
I highly recommend it. JWT authentication was also added so that users can register and login to empower themselves 
with create, delete and update rights. The back end is a small api built with django rest framework.
You can see the source code in its [github](https://github.com/dimyG/algorithms_project) repository. 

> ***Note***: Algozoom is currently hosted on heroku under a free tier and this means that the application goes
> to sleep if doesn't receive any traffic for 30 minutes. Awaking from sleep takes some time, so if you experience a delay 
> of a few seconds when you try to visit the website, know that you are awaking it. 





