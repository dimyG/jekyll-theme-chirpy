---
title: My experience as a CAE engineer
author: Dimitris Georgoulas
date: 2020-06-02 11:30:00 +0200
categories: [Career, Experience]
tags: [cae, career]
toc: true
comments: false
---

# Computer Aided Engineering 
CAE is probably an unknown term for most people coming from the web development world. It might say something to people 
with a pure computer science background but generally this is not the case. So, what is it? 
CAE stands for "computer aided engineering". It is a wide term that encompasses a lot of things. As per Wikipedia: 
> Computer-aided engineering (CAE) is the broad usage of computer software to aid in engineering analysis tasks. 
It includes finite element analysis (FEA), computational fluid dynamics (CFD), multibody dynamics (MBD), 
durability and optimization. It is included with computer-aided design (CAD) and computer-aided manufacturing (CAM) 
in the collective abbreviation "CAx". 

CAE is the use of specialized software for engineering analysis tasks. Not bad at all. This can be clarified with an example. 
Let's focus on the process of creating a new mechanical part, for example a new rocket part. The typical process, simplified for clarity, 
is something like the following:

Taking into account all requirements and constraints, the first step is to define the fundamental specifications of the new part. 
Having them we can proceed to the detailed design and creation of its 3d model in a CAD software. 
There are a lot of CAD software available, the most common of which exist for decades and are composed of millions of lines of code. 
Some examples are 
[CATIA](https://www.3ds.com/products-services/catia/?wockw=card_content_cta_1_url%3A%22https%3A%2F%2Fblogs.3ds.com%2Fcatia%2F%22), 
[NX](https://www.plm.automation.siemens.com/global/en/products/nx/) 
or [Inventor](https://www.autodesk.com/products/inventor/overview). 

When the new 3d model is ready, it is still completely untested. We have no idea about its behaviour under working conditions. 
We don't know how much stress it can absorb before cracking, how it vibrates under certain loading conditions or 
how much aerodynamic drag it generates. These things are the essence of engineering analysis. Before the advance of 
computers and specialized software, you would have to 
manufacture some prototypes and subject them to a series of physical tests. Load them until they crack to measure their strength, 
put them in aerodynamic tunnels to observe the airflow around them and measure various factors like the aerodynamic drag etc. 
This is an extremely expensive and slow process, especially if we have to repeat it for any new adaptation of the original design. 
The solution to this problem is of course, software. Specialized software. CAE software. 

The implementation of these engineering analyses by software becomes possible by the use of a set of mathematical methods 
and computational techniques. One of the most important methods is called Finite Elements Method (FEM). 
The concept is that you split the 3d model to a very large number of very small pieces (the finite elements). Their small
size and their specific geometric shape (parallelograms, triangles, tetrahedrons, cuboids etc.) make possible the 
calculation of their deformation under certain loading conditions. By combining the effect of each finite element, we can approximate 
the behaviour of the entire 3d model under the same loading conditions. 

 <img src="/assets/img/sample/shell_mesh.png" alt="shell_mesh" width="450"/>
 
This discretization of the 3d model, the creation of the finite 
elements or mesh as it is called, is performed by another family of software called _pre-processors_. Pre-processors receive the 
3d model from the CAD system and basically transform it to another form. They create the mesh, they set up the loading conditions 
defining the various forces applied to the part and finally they output this discretized model to a file. A specially formatted file that 
describes the finite elements and the forces applied to them. This file is the input to another 
family of software, the _numerical solvers_. A solver receives the meshed loaded model and calculates its behaviour 
using numerical methods to solve a set of equations that describe this behaviour. This behaviour is an approximation of 
the real one, since the equations are not solved analytically (exactly) but numerically (approximately). If the model is 
properly created though, the approximation is very good. In any case the simulations' results are always verified by physical 
tests. 

There are various types of solvers ([Nastran](https://www.mscsoftware.com/product/msc-nastran), 
[Abaqus](https://www.3ds.com/products-services/simulia/products/abaqus/), [LS-Dyna](https://www.lstc.com/products/ls-dyna), 
[OpenFOAM](https://www.openfoam.com/) etc.), each specialized to different types of 
analysis. One type of analysis calculates the strength of the part (Structural analysis), a different one its behaviour under vibrations (NVH analysis) 
and another one its aerodynamic properties (CFD analysis). This whole process of studying the characteristics of the part 
using finite element analysis is the job of a CAE engineer. The application of these methods and the use of the 
specialized software can be found in all major manufacturing industries (automotive, heavy machinery, aerospace, maritime, energy, etc.).

# My role at BETA CAE Systems
After graduating as a mechanical engineer from the polytechnic school of Aristotle University of Thessaloniki and 
finishing the obligatory in Greece, military service, I joined [BETA CAE Systems](https://www.beta-cae.com/) at 2009. BETA, based in my home city Thessaloniki, 
is one of the biggest software houses in the country, developing CAE software since the 90s. It is an amazing company 
that grows organically and steadily over the years. It is most famous for its flagship product called [ANSA](https://www.beta-cae.com/ansa.htm), 
a pre-processor used extensively by thousands of companies all over the world. Nowadays BETA develops a whole suite of software products 
targeting the global CAE industry.

I became a member of the customers support division to which I stayed for more than 5 years. 
As a support engineer in BETA you are like a swish army knife. You do many things. 
Setting up and implementing a wide range of CAE analyses using the whole CAE software stack, 
automating tasks with python scripts, debugging the software, writing documentation, converting client needs to software requirements, 
authoring technical CAE papers, performing technical presentations to the engineering teams of our clients and on international conferences, 
participating in clients projects and visiting their premises to offer on-site support and training. 

I gradually became familiar with many aspects of the automotive and maritime industries, especially on 
the structural design aspect of things, implementing engineering simulations across many disciplines 
(Structural, NVH, Optimization) using a great variety of software 
([ANSA](https://www.beta-cae.com/ansa.htm), [mETA](https://www.beta-cae.com/meta.htm), 
[Nastran](https://www.mscsoftware.com/product/msc-nastran), [Abaqus](https://www.3ds.com/products-services/simulia/products/abaqus/), 
[LS-Dyna](https://www.lstc.com/products/ls-dyna)). Since my department was focused on optimization solutions, I 
had the opportunity to gain extensive experience on optimization methods and tools 
([modeFRONTIER](https://www.esteco.com/modefrontier), [Optimus](https://www.noesissolutions.com/our-products/optimus), 
[Tosca](https://www.3ds.com/products-services/simulia/products/tosca/)) while further developing my programming skills.

I had also the privilege to meet many interesting people and see beautiful new places while visiting customers and 
attending conferences all over Europe and abroad.

Conclusively, I would say that my time in BETA was invaluable. I was able to contribute to the company's 
goals while having plenty of room for personal development too, especially as a junior engineer as I was when I first joined. 
But, about 5 years later, I felt that was time for me to move on to a different path. I would at last had the 
opportunity to combine two of my passions, programming and entrepreneurship, in what later became 
[my epic fail as a sports betting entrepreneur](http://127.0.0.1:4000/posts/my-epic-fail-as-a-sports-betting-entrepreneur/).  

# A small showcase
The following video is a small showcase of my work at BETA, back at 2012. It is a ships collision simulation and 
might be interesting to watch. Both ships are deformable, meshed with three zones of mesh refinement giving a total 
of 1.3 million finite elements. The simulated time is about 2 seconds, until the start of the spring-back effect. 
Collisions like this is not that common but they are possible especially near the harbors where lots of ships 
are packed in a small space.

<iframe width="560" height="315" src="https://www.youtube.com/embed/XxOQ_cpif9U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

