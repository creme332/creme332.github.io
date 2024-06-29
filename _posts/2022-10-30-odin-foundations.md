---
title: My experience with The Odin Project foundations course
categories : [Learning Journey]
tags :  [the-odin-project]
description: My experience with the web development foundations course by The Odin Project.
comments: true
mermaid: true
---
 
On 19th June 2022, I officially began my web development journey with the help of The Odin Project (TOP), a free open-source course. The course is divided into two sub-courses: a foundations course where you learn the basics of web development and a full-stack course where you learn to become a full-stack web developer. In this post, I will talk about my experience with the foundations course.
 
 > As you read this, keep in mind that my experience might not reflect the current state of the foundations course. Over time, there have likely been significant updates and changes. For instance, while WSL2 wasn't supported by TOP in 2022, it is now fully integrated in 2023.
{: .prompt-warning}

## Almost abandoning TOP
 
I must admit, as a Windows user,  I seriously considered finding another web development course after reading the following on TOP's website:
 
> **We [The Odin Project] can only support what is provided within the scope of our curriculum. We do not support native Windows or any version of Windows Subsystem for Linux (WSL) as a development environment.**
>
> -- [From the Installation guide](https://www.theodinproject.com/lessons/foundations-installation-overview#concerned-about-installing-a-new-os)
>
 
I was very hesitant to replace Windows with Linux because I did not want to reinstall all my apps. I was also using some Windows apps which were not available on Linux. Alternatives like dual booting and virtual machines did not appeal to me because of performance issues and the complexity to set it up.
 
After some research, I decided to go against the tide and install WSL and so far I am getting the best of both worlds : an easy-to-use Linux environment while being able to keep my Windows apps.
 
> For anyone in the same boat as me, I highly recommend [this lesson by Fireship](https://fireship.io/lessons/windows-10-for-web-dev/) to set up WSL easily.
{: .prompt-tip}
 
## The good
 
There are already hundreds of good reviews of TOP on the internet and I whole-heartedly agree with each of them. You can read some of them [on this Reddit post](https://www.reddit.com/r/learnprogramming/comments/u6rrz9/why_is_everyone_recommending_the_odin_project/).

Here are my favorite things about TOP:
 - **Unbiased**: TOP will direct you to the best learning resources on the internet irrespective of who made it. This sets it apart from typical YouTubers, who often exclusively endorse their own videos for learning purposes.
 - **Open-source**: Not having to spend money on an educational resource is great.
 - **Self-paced**: I was able to work at my own pace and spend as much time as I wanted on each section of the course.
 - **No hand-holding for projects**: There are no step-by-step tutorials on how to build each project. I was only only given the basic requirements for the project and I could implement as many or as few features as I wanted. Moreover, it is fascinating  to see everyone's interpretation of the project specifications and how they infused their personal flair into it.

## Aspects to consider
By the end of the foundation course, you will not become a great web developer but you should be able to create static websites with HTML, CSS, and JavaScript. This course is **only a stepping stone for the full-stack course** which you will have to take afterwards.

Here are some topics that I had to learn on my own since they are not taught in the foundations course at the time of this post:
- **UI/UX design** : The course will not teach you how to create a  website design from scratch or how to choose the correct color palette for your website. You will either have to learn these things on your own or take inspiration from existing websites on the internet.
- **Responsive design** : This topic is covered only in the later stages of the full stack course so unless you self-learn it, your websites will not look great on different screen sizes.
- **Search-engine-optimization** : You will not learn how to improve the discoverability of websites on search engines. This will limit internet traffic to your projects.
- **CSS animations** : This topic is covered only in the full stack course.

I concede that none of the above topics are important for a beginner but some of this knowledge was useful for me when working on projects.

Another downside of the foundation course is that some of the reading materials that TOP recommends, although high-quality, is bulky and can be mentally draining. I definitely took some time to go through all the necessary materials and avoid the temptation of simply skipping it.

Finally, it is worth noting that unlike some online courses **TOP does not give you any certificates** upon completion. However, this should not be a deciding factor when choosing a web development course since most online certificates are worthless.

## My statistics
 
The amount of time needed to complete the foundations course totally depends on the amount of work you put in. In my case, I took **2 months (43 days of work to be exact)**  to complete the foundations course (19 June 2022 - 20 August 2022). The most time-consuming part of the course is without any doubt the projects.
 
 ```mermaid
pie 
    title Time breakdown
    "Recipes": 20.9
    "Landing page": 9.3
    "Rock Paper Scissors": 18.6
    "Etch a sketch":  16.3
    "Calculator":  7.0
    "Theory" : 27.9
```

> The percentage represents the percentage of days (out of 43 days). For example, I spent around 9 days on the Recipes project.
{: .prompt-tip}

Here are some factors to consider about me before we dive into more details:
- I had previous programming experience in VB.NET, C++ and Python which probably made learning JavaScript easier.
- I skipped some of the additional resources found in the course.
- I was free at that time (no work/school) and on some days I managed to put in 8-10 hours of work.
- I devoted most of my time to the projects. Some projects took as long as 1 week of work.
 
### Timeline

#### Recipe project (9 days)
 
**27 June** : Built project with pure HTML.
 
**28 June** : Built 2nd version of project with pure HTML and CSS.
 
**2-3 July** : Built 3rd version with Flexbox for practice.
 
**21-25 July** : I was not satisfied with the previous version and started from scratch again to build a 4th version.

<img src="https://github.com/creme332/my-odin-projects/raw/main/odin-recipes/iterations/iteration6img.png"  height ="300" width ="500" alt = "Image of Rock Paper Scissors project">

#### Landing page project (4 days)
 
**7-10 July** : Built a responsive Landing page with flexbox and media queries.
![](https://github.com/creme332/my-odin-projects/raw/main/landing-page/landingpagegif.gif)
 
#### Rock Paper Scissors project (8 days)
 
**12-19 July** : I had to learn CSS animations from Youtube to build the project. This is by far my favourite project: I really liked the GUI and the animations.
 
<img src="https://github.com/creme332/my-odin-projects/raw/main/rps-game/gifs/gif1.gif" height ="300" width ="300" alt = "Image of Rock Paper Scissors project">

#### Etch-a-sketch project (7 days)
 
**25-31 July** : I implemented "complex" features like Paint bucket fill, Undo/Redo. This was a fun project to work on.

<img src="https://github.com/creme332/my-odin-projects/raw/main/etch-a-sketch/iterations/iteration1.gif" height ="500" width ="500"  alt = "Image of Etch-a-sketch project">

#### Calculator project (3 days)
**13-15 August** : I did not want to spend too much time on this project but I still wanted to create something totally different from others. I finally settled for a basic version of an abacus.

<img src="https://github.com/creme332/my-odin-projects/raw/main/calculator/assets/img/iterations/abacus.gif" height ="300"  width= "300" alt = "Image of abacus project">

This project served as inspiration for my [advanced abacus project](https://creme332.github.io/abacusLite/).
 
> You can view all my projects [here](https://creme332.github.io/my-odin-projects/).
{: .prompt-tip}
 
## Advice for beginners
- Take your time to write a good README for each of your projects. Ensure that you have at least some screenshots of your website in the README. A link to your deployed website is not enough since the service you have used to deploy your website may stop working.
- Join the TOP discord channel and seek feedback on your projects.
- Do not feel intimidated by the projects of other people in the Solutions section of each project page.
- Take breaks (as much as you need) to avoid burning out.
- Create your own github [project template](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-template-repository) to save time on setting up your projects.
 
## Conclusion

The foundations course has helped me get my feet wet in web development and I would highly recommend it to anyone who wants to get started. I will now proceed to the Full Stack JavaScript course (by The Odin Project of course) to take my web development skills to the next level.