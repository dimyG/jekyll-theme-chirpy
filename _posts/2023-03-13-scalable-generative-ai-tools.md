---
title: Scalable generative AI tools
author: Dimitris Georgoulas
date: 2023-03-13 11:30:00 +0200
categories: [web-development, django, javascript]
tags: [machine learning]
toc: true
header-img: /assets/img/posts/jinifai_showcase.png
description: "Jinifai MVP is a scalable web application offering generative AI tools that can be used to generate images. It is built with django and fastAPI microservices and is deployed on AWS. The wen client is made with React and Redux."
---

 ![Desktop View]({{ "/assets/img/posts/jinifai_showcase.png" | relative_url }})  

# Jinifai MVP
Jinifai is a side project I worked on, to play with diffusion models and microservices. It is a scalable web 
application offering generative AI tools for creating images. It is build with a microservices architecture. The MVP is 
really minimal but the main backbone of the system is in place, and it is easy to enhance it with additional features and services.
Given enough time and resources, it can become a full-fledged, scalable generative AI platform that implements all the features of 
the [stable diffusion webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui).

This is the high level architecture of the system:

 ![Desktop View]({{ "/assets/img/posts/jinifai_mvp_architecture.png" | relative_url }})

- Auth service (django, [GitHub repo](https://github.com/dimyG/web_ai__auth))
: Responsible for user authentication and authorization using JWT tokens.
- Payments service (django, [GitHub repo](https://github.com/dimyG/web_ai__payments))
: Currently, it implements a fake payments system.
- Pre inference service (fastAPI, [GitHub repo](https://github.com/dimyG/web_ai__pre_inference))
: Responsible for rate limiting users based on tier (and future preprocessing tasks).
- Inference service (Runpod, [GitHub repo](https://github.com/dimyG/web_ai__web_client))
: Inference is serverless. The minimum number of workers is 0 (for cost reasons). This means that you might experience a 
cold start and a delay of a few seconds when you first generate an image.  
- Web client (React, Redux, [GitHub repo](https://github.com/dimyG/web_ai__web_client))
: The web client is a single page application, deployed on S3 and served by CloudFront (through AWS Amplify).

Auth, payments and pre inference services are deployed on an AWS ECS cluster. Inference is deployed on 
a [Runpod](https://www.runpod.io/) serverless API. There is an AWS API Gateway in front of the ECS cluster. 
The microservices communicate with each other by publishing and consuming events on rabbitMQ. 
There is a postgres RDS instance with one database for each service. Redis is used as a cache store 
for all services. Static files are stored on S3 and served through Cloudfront CDN. 
CI/CD is implemented with GitHub actions.