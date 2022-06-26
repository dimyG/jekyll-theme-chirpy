---
title: NYU 2021 Deep Learning course overview and notes 
author: Dimitris Georgoulas
date: 2022-06-21 11:30:00 +0200
categories: [Machine Learning, Deep Learning]
tags: [machine learning]
toc: true 
---

 ![Desktop View]({{ "/assets/img/NYU_2021_DL_course/course01.JPG" | relative_url }})  

# TLDR
With one word, did I like it? Absolutely. In my opinion, the most important benefit from the course is LeCun’s distilled wisdom. 
You will understand the current state of deep learning especially with regards to self-supervised learning, challenges, 
proposed solutions and future plans. The main drawback? Maybe LeCun’s teaching style is not so student friendly 
as Andrew Ng’s, at least for me. But after a while you are getting used to it and the rewards far outweigh the costs. Here are 
[my notes](https://docs.google.com/document/d/1D6-s02WURolebjSA3qNQcvhIGqYz69jS/edit?usp=sharing&ouid=101182743259035030658&rtpof=true&sd=true).
(They contain a lot of images so the docx file is quite large...)

# The candidates
Well, this year I made at last the decision to focus to AI, and I’m so grateful for this opportunity. A while back, 
I was in search for a good online deep learning course to dive into and my search concluded to three final candidates. 
Of course, these courses are not mutually exclusive, on the contrary, but if your time is limited, you must make a choice. 
Here they are:
1. [NYU Deep Learning SP21](https://www.youtube.com/playlist?list=PLLHTzKZzVU9e6xUfG10TkTWApKSZCzuBI) 
2. [Deep Learning Stanford CS231N](https://www.youtube.com/playlist?list=PLSVEhWrZWDHQTBmWZufjxpw3s8sveJtnJ) 
3. [Coursera Deep Learning Andrew Ng](https://www.youtube.com/playlist?list=PLkRLdi-c79HKEWoi4oryj-Cx-e47y_NcM)

Can we just pause for a moment and just admire the fact that all this knowledge is freely available to anyone out 
there interested in absorbing it? What a gift!

# Why NYU?
Anyway, I had already watched the famous Machine Learning course from Andrew NG and I must say, his teaching style is indeed amazing. 
This made me think twice before switching to someone else for Deep Learning, but nevertheless I decided to do it. 
And there was a very important reason: Self Supervised Learning. You see, all three courses, as well as most of any 
other you can find online, contain a lot of common things: MLPs and backpropagation, CNNs, RNNs, GANs, Transformers, 
classification examples and some practical tips. Whatever course you choose, you will hear about these things. 
It’s just a matter of teaching style preference at the end. In most of them though, the common theme is supervised learning. 
NYU’s course on the other hand, shines by focusing on self-supervised learning too, a subfield of deep learning which according 
to many important people, preeminently Yann LeCun, is a fundamental pillar of the future of AI. It also resonates 
a lot with my humble but genuine [intuitions]({% post_url 2020-06-04-thinking-about-intelligence %}) on intelligence. 

The course has two instructors, Yann LeCun himself and Alfredo Canziani focusing on theory and practice respectively 
(but not exclusively). Yann LeCun needs no introduction while Alfredo is an assistant professor of CS at NYU. 
The course is very wide, covering lots of things and in most of them, it goes deep too. It is quite large, 
containing about 50 hours of information-dense content. Kind of a marathon, not a sprint. 
Alfredo has created a [github page](https://atcold.github.io/NYU-DLSP21/) for the course containing lots of resources 
including some super useful jupyter notebooks with ready to run code. He even responds to youtube comments! 
There are also guest instructors from the FAIR lab (Facebook AI research lab) who present their latest work on 
computer vision and NLP, so you get a glimpse of industry work too. 

# Course's main theme
One of course’s main themes, is ways to handle uncertainty. Lecun’s opinion is that a basic component of an intelligent agent, 
is its ability to make good predictions. This requires a world model, a module that receives the state of the world 
and an action, and can predict some future state of the world. One main challenge in building these models though, 
is that the world is stochastic. It contains a lot of uncertainty. A typical example is the falling pencil. 
If I hold a pencil upright on top of a desk and I let it fall, you can’t predict where exactly it would land. 
There are many different equally plausible futures. We don’t have a complete knowledge of the world state, 
so we can’t do a deterministic decision. In these cases, one input has many plausible outputs. 
One state of the world can be followed by one of several plausible future states. So how can we handle this in DL? 
This is the problem energy-based models are trying to solve. Handling uncertainty. 
More specifically, learning to do good predictions under uncertainty. 

And equally important is the fact that the agent should be able to learn this world model mainly by observation, 
the same way animals do, using unlabeled data. This is the reason why self-supervision is crucial. 
The labels come from observations. You predict the next state, wait a bit, the next state occurs, and you have your label. 
Or you hide parts of a sentence, and you try to predict the missing words which act as your labels. And more stuff like this. 

>Spoiler alert: According to LeCun the best path towards good predictive world models that can handle uncertainty, 
is joint embedding architectures trained with non-contrastive regularized methods (see Barlow Twins and VICReg). 
If you take the course you will be able to understand why and how. 

# A personal favorite
One particularly interesting part of the course is the planning and control lectures where Alfredo presents a research 
project he worked on as member of LeCun’s team. They developed an autonomous agent, for a simplified self-driving type of situation. 
The agent learns a predictive world model that handles uncertainty, and a controller (or policy) that generates actions. 
Notice, that there is no simulation environment where the agent can try things and get rewards. It only learns from observation. 
It is not an RL case. It is model predictive control where the agent plans for a horizon in the future and gets a cost after every action. 

 ![Desktop View]({{ "/assets/img/NYU_2021_DL_course/PPUU.gif" | relative_url }})  

> One remark not mentioned in the lecture: This is a project from 2018. Now they would not do it exactly like this. 
> In this project the agent predicts in pixel space which means that the learned representations of the world must 
> contain a lot of details so that they can predict a full video frame. Instead, it would be better to use a world 
> model that predicts in the representation space which is much lower in dimensions so you don’t have to learn to 
> predict all the irrelevant details.

> Another remark not mentioned in the lecture: Hierarchical JEPA models (joint embedding predictive architectures) is 
> a new proposal from LeCun for creating autonomous agents. They predict in representation space, and they can make 
> hierarchical action plans which means longer plans into the future. But it is a yet untested approach (as of 2022) 
> that has a lot of challenges. 

# Challenges
Bear in mind though, that the course is challenging and not all lectures are of the same quality in terms of clarity, 
but I think this is expected in such a large course.

LeCun tries to explain things through his energy-based framework, and this might be a bit confusing, at least in the beginning. 
There are also parts where he explains things very abstractly, defining them within a formal framework, 
so you must be patient to see where he is going to. There are lectures where he provides mathematical proofs for some 
fundamental concepts in deep learning. You might love them or hate them depending on your math skills and interest. 

For me, there was literally no lecture I watched continuously to the end in one go. Perhaps this is how I learn things, 
but I had to pause and think, take notes and rewatch many parts, to feel comfortable enough to continue. 
No wonder, it took me more than three months to finish it, with some full-time studying days. And still of course, 
there are many things which aren’t fully understood. This is a lifelong journey after all. And practice makes all the difference. 

# Prerequisites
Are there specific prerequisites? Yes, there are. It is necessary to know at least the fundamentals of machine learning
and linear algebra. There are some great courses on these topics, like the ML course from Andrew NG 
and the [Essence of linear algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) from 3Blue1Brown. 
[This](https://www.youtube.com/watch?v=YrHlHbtiSM0&list=PLUl4u3cNGP61iQEFiWLE21EJCxwmWvvek) short list from Gilbert Strang 
of MIT is also an excellent high level overview of linear algebra. There is a long version too, but I didn't have the opportunity to watch it. In my opinion, it’s also 
helpful to already have some fundamental understanding of how backpropagation works. 3Blue1Brown 
has a short but great [list](https://www.youtube.com/watch?v=aircAruvnKk&list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) 
on backpropagation too, where he explains the whole process in a very intuitive way (as usual).

# My Notes
I have a lot of notes from the course which you can find [here](https://docs.google.com/document/d/1D6-s02WURolebjSA3qNQcvhIGqYz69jS/edit?usp=sharing&ouid=101182743259035030658&rtpof=true&sd=true). 
Bear in mind that the language is not formal (to put it mildly) and they might contain a few errors. Feel free to comment where you want. 
Also notice that I regularly update them. 

That's all for now, thanks for reading!