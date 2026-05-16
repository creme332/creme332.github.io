---
title: The Things I Had To Learn Outside my CS Degree
categories: [Learning Journey]
tags: [git, github, linux, docker, latex, deployment, collaboration, productivity]
description: Reflections on the practical software engineering skills I had to learn outside my computer science degree.
comments: true
---

In this post, I reflect on the things that my three-year degree in computer science did not teach me. These are the things that I had to learn by myself through online resources and experience. I am aware that a Computer Science (CS) degree cannot teach everything due to limitations in time and resources, and this post is not intended to criticize or evaluate the usefulness of a CS degree.

## Version Control

One of the most important concepts that was not taught was version control. Version control is a way of tracking changes made to files. It allows you to "go back in time", keep parallel versions of the same file, and much more. It helps avoid situations where you create files like "file1.txt", "file1-edit.txt", and "file1-final-final.txt". The most common tool for version control is Git.

I first learnt about Git while self-learning through The Odin Project in my first year of university. Ever since, I have used it for all my assignments and projects. I also pushed my Git repositories to GitHub to have a cloud backup.

I highly recommend using Git and GitHub for your assignments, especially when writing your dissertation.

## Reading Large Codebases

At university, you are usually working from scratch for assignments, and most coding projects stay under 5k lines of code. Consequently, you are rarely exposed to real-world projects, and the first time you view one and try to navigate it, you can feel completely lost.

During my internship, I remember having to navigate through tens of files just to understand the architecture of the project and its abstractions in order to fix a simple UI bug. Also, in real life, you are mostly reading code that you have never written, which is a lot harder than reading your own code.

This is a skill that comes with experience. One way to get used to reading large codebases is through open-source contributions on GitHub. Nowadays, coding agents can also help you understand the structure and overall architecture of a project.

## Code Collaboration

Working with other people on a coding project is quite cumbersome at university because, most of the time, no one really knows how to proceed: who writes what code, how to merge the code, who reviews the code, whether coding standards are needed, and so on. A CS degree exposes you to these situations but does not really teach you how to deal with them. I have seen students collaborate on Google Drive and WhatsApp for coding projects when platforms like GitHub exist.

I was fortunate enough to learn how to use GitHub before I even began my CS degree. However, I still had to figure out things like reviewing pull requests, fixing merge conflicts, and understanding Git workflows once I started working in teams. Furthermore, skills like bug tracking and writing code documentation are usually only learned when working in the trenches.

## Deployment and Infrastructure

My degree taught me how to write software, but not necessarily how to ship it. For example, I was taught how to build full-stack websites, but not how to deploy them for other people to access on the internet. Concepts like SEO and domain hosting were mostly brushed off.

I had to learn the following by myself:

- Docker
- CI/CD with GitHub Actions
- Cloud hosting
- Linux servers
- PaaS providers like Vercel and Heroku

## Touch Typing

Touch typing is the skill of typing with both hands without looking at the keyboard. It is a skill that is rarely taught but can severely limit the productivity of CS students if neglected. For example, slow typing speeds mean that you have less time, or sometimes no time, to debug your code during lab practical tests. Moreover, it also means that you are slower at taking notes. If you are going to spend most of your life in front of a screen, it is essential to invest some time into achieving a good typing speed.

I recommend using online typing tests like Monkeytype to reach a decent typing speed of at least 50 WPM. In my case, I started touch typing back in high school and practiced on various platforms like TypeRacer, 10FastFingers, and Keybr. I can now achieve average speeds of 80 WPM on normal text with capitalization, numbers, and punctuation. A major advantage of touch typing is being able to rapidly digitize and back up all my notes instead of keeping handwritten ones.

## Academic Tooling

The academic world is highly dependent on LaTeX, a typesetting system. A large number of published research papers are written in LaTeX because it improves formatting and makes documents look more professional. I highly recommend learning it because it makes your reports and assignments look significantly more polished.

Citation management is another important concept, especially when writing your dissertation. In LaTeX, you can use BibTeX. In a single file, you can define all your citations and then reference them in the main body of your document. Both your bibliography and inline citations are guaranteed to stay consistent. 

You can also customize the formatting style according to your university's requirements. In my case, my university used a modified version of the Harvard referencing system, and I had to create [my own style](https://github.com/creme332/uom-harvard-latex) that matched the university's standards. Most of the time, however, your university will already provide a LaTeX template or citation style.

## Conclusion

My degree gave me strong theoretical foundations, but many practical skills had to be learned independently through internships, personal projects, open-source resources, and experience.

This post is not an exhaustive list of all the skills that a CS degree does not teach. I also did not cover skills like debugging, communication, and independent learning, which are equally important and mostly learned through experience.

One of the resources that I used to bridge the gap was [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/). It is a free resource from MIT that teaches many of the things covered in this post, such as shipping code, agentic coding, command-line tools, and much more.