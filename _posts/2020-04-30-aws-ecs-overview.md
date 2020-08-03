---
title: AWS Elastic Container Service overview 
author: Dimitris Georgoulas
date: 2020-04-30 11:30:00 +0200
categories: [Web-Development, Miscellaneous]
tags: [aws]
toc: true
---

## Containers in AWS
Let's begin with some context. When it comes to deploying your containerized service to AWS, you can choose between a variety of different services, each 
one with its own strengths and weaknesses. 

**AWS Elastic Beanstalk**  
Elastic Beanstalk is ideal if you want to leverage the benefits of containers but just want the simplicity of not 
having to manage the underlying infrastructure yourself. It will automatically handle all the details such as 
provisioning an ECS cluster and placing the containers in it, load balancing, auto-scaling, health monitoring and deployment. 
You simply specify which container images are to be deployed, the CPU and memory requirements, the port mappings, and the container links. 
One downside though is the lack of flexibility. For example, it supports a single ECS Task definition per environment. 
This means that all cluster instances must run the same set of containers, which means that your containers are scaled all 
together as a single unit. If you have both web and worker containers and you only 
want to scale your workers, then you have a problem. Since both the web and the worker live in the 
same ECS task definition, they can't be scaled independently. 

**AWS Elastic Container Service (ECS)**  
ECS is a container orchestration platform much like Kubernetes. 
It gives you great flexibility, but at the expense of provisioning and managing the underlying infrastructure like 
the instance type, AMI, EBS size, VPC subnet, security groups, and more. Not an easy task especially for people without 
much experience in managing AWS infrastructure. Luckily, from 2017 and 
onwards, you can use ECS with one of two different "modes" or launch types as they are officially called. There is the 
legacy EC2 launch type and the new Fargate launch type. With Fargate, you don't have to deal with the underlying EC2 instances. 
In a sense it gives you the flexibility of ECS combined with the simplicity of not having to manage the underlying 
infrastructure yourself. 

**AWS Elastic Kubernetes Service (EKS)**  
If you prefer to run your application in a kubernetes cluster on AWS, you can choose to manage the infrastructure 
yourself or get an automatically provisioned, managed Kubernetes control plane with Amazon EKS. Either way, you get 
integrations to AWS services like VPC, IAM, and service discovery as well as the security, 
scalability and high-availability of AWS. It is important to note that Fargate, after ECS, is extended to EKS as well, 
enabling kubernetes developers to transform a standard pod definition into a Fargate deployment making it possible to
run containers in a serverless and nodeless environment. 

AWS Fargate  
AWS Fargate, introduced by Amazon in 2017, is not a separate service. Rather it is a technology that can be used within ECS 
and EKS and allows you to run containers without having to manage servers or clusters. 
It is a layer on top of ECS and EKS, that abstracts the infrastructure and enables users to 
focus on the desired state of the application. Here is a very informative series of posts about Fargate and how it works within ECS and EKS:
- [the-evolution-of-serverless-container-platform-on-aws-fargate](https://thenewstack.io/the-evolution-of-serverless-container-platform-on-aws-fargate/)
- [aws-fargate-through-the-lens-of-kubernetes](https://thenewstack.io/aws-fargate-through-the-lens-of-kubernetes/)
- [how-aws-fargate-turned-amazon-eks-into-serverless-container-platform](https://thenewstack.io/how-aws-fargate-turned-amazon-eks-into-serverless-container-platform/)

## ECS in a nutshell 
**Launch types**  
You can run it either on EC2 launch type or in Fargate. In the first case you have to manage the underlying infrastructure 
yourself, while in the later case the underlying resources the cluster runs in, are managed by amazon.

**Task Definitions**  
To prepare your application to run on Amazon ECS, you create a task definition. The task definition is a text file, 
in JSON format, that describes one or more containers, up to a maximum of ten, that form your application. It  
declares the resource requirements such as CPU units, memory, network ports and others.

**Container Instances**  
The containers you describe in a task definition live in a container instance. What the container instance actually is, 
depends on the launch type. In EC2 launch type, the container instance is a an EC2 instance that is running the 
Amazon ECS container agent and has been registered into a cluster. Tasks using the Fargate launch type are deployed 
onto infrastructure managed by AWS.

**Services**  
After you have your task definitions, you can create services from them to maintain the availability of your desired 
tasks. To scale your containers, you have to scale your tasks which are scaled by their service. 
If any of your tasks fail or stop for any reason, the Amazon ECS 
service scheduler launches another instance of your task definition to replace it in order to maintain the desired 
number of tasks in the service.

**Cluster**  
An Amazon ECS cluster is a logical grouping of tasks or services. If you are running tasks or services that use the 
EC2 launch type, a cluster is also a grouping of container instances. 

**Container agent**  
The Amazon ECS container agent is a process that runs in the container instance. It allows container instances to 
connect to your cluster. Each EC2 instance participating in the cluster runs an agent to talk to the ECS control plane. 

**Resources and Tags**  
Amazon ECS resources, including task definitions, clusters, tasks, services, and container instances, are assigned 
an Amazon Resource Name (ARN) and a unique resource identifier (ID). These resources can be tagged with values 
that you define, to help you organize and identify them.

**Service Discovery for Amazon ECS**  
There is a process for making a service privately available (reachable 
from your other VPC resources) and a different process for making it publicly available. In both cases you 
can’t use a specific IP for your DNS records since the underlying container instances might change during the 
lifetime of your services. You can make your service privately available with AWS Cloud Map which can point to the 
correct IP every time. For making it publicly available, the proper way is to put it behind a 
load balancer. The load balancer has a DNS name to which you can point your DNS records. 

**EC2 launch type tips**  
- The default user created by ECS in the EC2 instances is _ec2-user_. You can ssh into them as that user. 
- The environment variables specified in the task definition, only have effect when the container runs by the service which 
controls that task. They will not be 
available if you manually run a container with a custom command. In this case you have to specify the necessary environment 
variables in the docker run command. The easiest way to do it, is to upload an env file to your container instance and 
point to it with the run command.
- If you modify a task definition you have to update its service so that it uses the new task definition.
- If you push an updated container image with the same name to your docker registry and you want to update your services 
with it, you can just restart the service that controls the task with that container, enabling the _EnforceDeployment_ option. 
- When you create an ECS cluster, an Auto Scaling Group is created for it, with the proper number of EC2 instances. 
When you want to close your cluster temporarily you can edit it and set the desired instances to 0. 
When you want to start it again set it to what it was.

