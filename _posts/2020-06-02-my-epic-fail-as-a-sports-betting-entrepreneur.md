---
title: My epic fail as a sports betting entrepreneur
author: Dimitris Georgoulas
date: 2020-06-02 11:30:00 +0200
categories: [Career, Experience]
tags: [zakanda, career]
toc: true
comments: false
---

## TLDR
The only way to survive as a company in the sports betting industry is to go on bookmakers side. If your vision is 
to help players make money then you are doomed to fail. I had no interest to this kind of business model so I completely 
abandoned the industry after spending a lot of valuable time and effort. What I gained though is experience, both
in the technical and in the entrepreneurial realm. No failure comes without some gains after all. 

## The lucky start
About a decade ago in the beginning of the 2010s and for a couple of years, I was lucky enough to systematically make money from sports betting as a player. 
I was neither a genius nor a professional, just lucky as I later understood. So when I quit my full time 
job on 2015 and was struggling to pick a web service business idea that I would personally develop, 
picking the sports betting industry came a bit naturally to me. I was playing regularly, I was winning and I wanted 
a way to monitor my performance. I will build a monitoring tool, I thought, and not only that but I will make it 
available for anyone else that needs it. Each player will have a profile and all the statistics would be 
publicly available if he so desires. The best players will stand out and anyone will be able to follow them and make money 
too. They would even be able to charge for their advices and my web service would get a commission from the monthly 
subscriptions. It seemed a pretty solid business model to me at the time. I couldn't be more wrong.. 

## Coding wearing blinkers
With no prior web development experience but armed with a ton of will, energy, persistence and an ever growing thirst 
for knowledge, I started designing the system which would later become [zakanda](https://www.zakanda.com/). Such was my 
desire to apply my new web development skills that I didn't even thought about creating a [lean canvas](https://leanstack.com/leancanvas) for my business. 
Not only that but I didn't event do a proper market research before starting this new entrepreneurial endeavor. I just wanted to dig into the code. 
 
 ![Desktop View]({{ "/assets/img/sample/matrix02.png" | relative_url }})  
 
Anything else was a waste of time. I leave the technical details for [another post]({% post_url 2020-06-03-zakanda_technical_overview %}), but in a few words I can say that 
as a complete beginner as I was at the time, I made both good and bad technical decisions. The most immature decision though 
and one that cost me a lot of time down the line, 
was to to make zakanda a single page application. This choice would be amazing if it wasn't the fact that I 
didn't use a proper front end framework like React, Angular or Vue. I just did it using [pjax.js](https://github.com/defunkt/jquery-pjax), 
AMD modules and [require.js](https://requirejs.org/). Which meant that in many occasions I had to reinvent the wheel..
 
 ![Desktop View]({{ "/assets/img/sample/shoot_foot.jpg" | relative_url }})  
  
Making a single page application to work properly without the proper tools, made me a lot wiser in the process, so the extra effort 
wasn't for nothing. Anyway, this is how more than a year passed. With me coding and studying without pausing for a moment to think where I am going or
to examine the core of my business model. I was just enjoying to code. 

## Facing the truth 
Fast forward a bit, with about 700 registered users zakanda was beginning to form a community of players. Around the time 
of the launch and as the main features were implemented I was spending more time researching the ins and outs of the betting industry 
and gradually the sad truth started to be revealed. This truth is simple and absolute. It is not possible to 
systematically win in sports betting if you bet on the mainstream bookmakers as most people do. The only way for a player 
to be a systematic winner is to bet on the so called value bets or take advantage of arbitrage opportunities. I will probably explain both in 
another post, but what's important for you to know is that identifying these opportunities is not an easy task. It requires a combination of 
time, specialization, inside knowledge and software. But even if you have the skills and you are willing to put in the effort, still 
you are not going to win. Why? Because bookmakers will block you. That's it, game over. They have specific algorithms in place that search 
for systematic winners. Once they identify someone they simply block their account. It is not against the law and of course 
you agree in the Terms and Conditions that they can block your account without explanation whenever they want. There are 
some alternatives like the betting exchanges in which you bet against other players, not against a bookmaker, but the betting volume is not large 
enough to support a lot of players.

## The moral choice
So, I was building a web service that encouraged mainstream people to bet in order to win, but now I knew that that was just not possible. 
And not only that but the most reliable way for my web service to make money, was to affiliate with the bookmakers so that 
I make profit from my players' loss!!! There was no way I would go that way. I simply couldn't do this, so for a time I 
was thinking about modifying the whole concept of my web service to make it more like a game where players could bet 
with virtual money and have fun with internal competitions and stuff. There were probably ways to make such a service to be 
profitable, but I just wasn't excited with it and had grown a natural dislike for this industry as a whole. So more than two 
years after starting this endeavor, I decided to abandon zakanda and the betting industry along with it. Or maybe not?    

## A last try
During the final moments of zakanda and as I was trying to find other ways to make a profit out of this whole effort, I 
started investigating another idea. I already mentioned the betting exchanges in which the players bet against each other. 
There are three or four of these exchanges that are of reasonable size. Apart from them there are a couple of bookmakers that advertise themselves as 
"winner friendly", meaning that they don't block winners. I already knew that although it is very difficult to be a professional 
winner, it is not impossible. There are people that manage to do it. So, I thought of creating something that would be 
exciting enough and also not against my morals. It would be an automation service offered to web services like zakanda, 
that have professionals in their ranks. You as a user would follow an expert in any of these services and whenever this 
expert made a bet, my web service would copy this bet automatically on your behalf in your linked account in the betting 
exchanges and the winner friendly bookmakers. Speed was of great importance in such a service since when a specific betting choice 
receives a lot of betting, its potential return starts to decrease making it less appealing. I also found a perfect name: Bettingun. 
This time though I wouldn't write a single line of code before validating the idea. I secured API licenses from betting exchanges, 
I spoke with potential customers, I assembled a small team, we were selected for the acceleration program of the [OKthess incubator](https://okthess.gr/en/) 
and... I finally decided to abandon this project too!

 ![Desktop View]({{ "/assets/img/sample/sorry.jpg" | relative_url }})  

The reason was that the concept had a major flaw. I knew from the beginning that the mainstream 
bookmakers would not be willing to give access to their APIs for a use case that brings them a huge stream of winners. 
I expected though that we could cooperate with the few winner friendly bookmakers. What an illusion.. 
When they learned about Bettingun they refused to give API access to their betting engine. This meant that we were left only
with the betting exchanges the combined betting volume of which, is not high enough to support a big 
number of players. So no matter how good our system might have been, it would only have value for a handful of people 
and its profits would be limited. I thus decided to stop it in the early stages so that we don’t waste time and resources without 
a really significant goal.

## Back to the beginning
Some years and a lot of effort later I was back to the beginning. Still with a lot of energy and will, wiser in many aspects and 
with plenty of battle scars, contemplating about the future ventures. One thing is for sure. Whenever I enter the 
entrepreneurial realm again, I will do it cautiously. 

Farewell zakanda and bettingun, you will always have a place in my heart...


 

  