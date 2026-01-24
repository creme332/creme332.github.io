---
title: Using Anki to Study Computer Science
categories : [Learning Journey]
tags: [anki, computer science, spaced repetition, learning systems, self-study]
description: Reflections and practical lessons from using Anki to study computer science.
comments: true
media_subpath: /assets/anki/
---

[Anki](https://apps.ankiweb.net/) is an open-source flashcard app that uses spaced repetition. The idea behind spaced repetition is to space out your revision so that you revise just before you are about to forget, so as to combat the forgetting curve. Anki simplifies the process by determining what cards to show or hide at a given point in time using a spaced repetition algorithm. It is commonly used for learning new foreign languages and by students studying medicine. For a better overview of Anki and spaced repetition, I recommend reading this page from the Anki manual: [https://docs.ankiweb.net/background.html](https://docs.ankiweb.net/background.html).

I've used Anki for several years to study for both my A-levels and my bachelor's degree in Computer Science (CS). In the process, I achieved a 4.0 GPA without cramming. In this post, I document my Anki workflow and the things that I have learnt after six years of using Anki. This is not a tutorial on Anki or on studying computer science. While the post focuses mainly on Anki, many of the tips shared are transferable to other flashcard apps.

## Use Cases for Flashcards

This section will be split into different subsections where I detail the type of knowledge that is worth converting to flashcards, while also showing some sample flashcards from my own collection. Please note that the effectiveness of flashcards depends on your ability to convert the right notes to flashcards. They work well for some modules and are much harder to use for other modules.

### Theory

Flashcards are most effective for memorizing theory. Thankfully, in my experience, nearly all CS modules contain a lot of theory that is examinable. Examples of theory-heavy modules where I used Anki are Software Engineering and Project Management, Networking, and Systems Security. For such modules, I created cards that follow these formats:

- Definition of X / What is X
- List Y features of X
- List Y advantages/disadvantages of X
- Give Y differences between X and Y
- Give X examples of Y

<img src="theory-cards/beta-testing.png" height ="446" width="702" alt = "Anki card asking definition of beta testing">

<img src="theory-cards/congestion-control.png" height ="446" width="702" alt = "Anki card on congestion control">

<img src="theory-cards/oop.png" height ="446" width="702" alt = "Anki card asking definition of object-oriented programming">

<img src="theory-cards/pad.png" height ="446" width="702" alt = "Anki card asking definition of packet assembly/disassembly">

### Diagrams

Flashcards can also be used for learning a diagram. It can help you precisely recall components or the flow of a diagram by hiding parts of it. While learning diagrams by heart is rarely required in CS, I did encounter some modules where it was required and at times you might want to create your own diagrams for revision. 
 
 > Use the [Image Occlusion Enhanced](https://ankiweb.net/shared/info/1374772155) add-on to hide parts of a diagram.
{: .prompt-tip}

<img src="diagram-cards/geometric-projections.png" height ="446" width="702" alt = "Anki card on geometric projections">

<img src="diagram-cards/magnetic-disk.png" height ="346" width="502" alt = "Anki card on magnetic disk">

<img src="diagram-cards/sr-flip-flop.png" height ="446" width="602" alt = "Anki card on SR flip flop">

### Maths/Logic

In my experience, modules that involve a lot of calculations or logic (e.g. Computational Maths, Formal Logic) can be studied effectively with flashcards. In this case, you need to create flashcards that test your understanding of:

- **Theory/Concepts**
- **Formulas**: Create cards that checks if you remember the formula or even the meaning of each term in a formula. 
- **Proofs/Algorithms**: Rather than trying to learn an entire algorithm in a single flashcards, prefer smaller flashcards where you can learn different parts of an algorithm. The `Cloze` note type is essential for hiding intermediate steps of calculations in example-based questions.
 
> Anki supports both MathJax and LaTeX which allows you to create well-formatted equations in Anki itself. See the manual for more details: [https://docs.ankiweb.net/math.html](https://docs.ankiweb.net/math.html).
{: .prompt-tip}

<img src="maths-cards/determinant.png" height ="446" width="702" alt = "Anki card on determinant formula">

<img src="maths-cards/matrix-dominant.png" height ="446" width="702" alt = "Anki card on diagonal dominance of matrices">

<img src="maths-cards/nfa-proof.png" height ="446" width="702" alt = "Anki cards on proof of NFA">

<img src="maths-cards/normal-distribution.png" height ="446" width="702" alt = "Anki cards on the effect of increasing variance in normal distribution">

<img src="maths-cards/proofs.png" height ="446" width="602" alt = "Anki card on proofs in formal logic">

<img src="maths-cards/regex.png" height ="446" width="602" alt = "Anki card on regular expressions">

### Programming

Modules that involve coding are harder to learn with Anki because coding and rote memorization do not go well together. Rote memorization of code snippets from lectures is futile because there are multiple ways to write the same code and you are very likely to make mistakes vomiting code for exams.

The learning objectives of programming modules are typically:

- Writing code
- Dry-running a program
- Remembering programming syntax
- Understanding and applying data structures and algorithms
- Learning theory like object-oriented programming, design patterns

For learning theory, the same guidelines given in the first section apply.

For Anki to work effectively for programming, you need to have prior understanding of all code that you are trying to learn.

To test your understanding of an algorithm (e.g. bubble sort), rely on example-based flashcards where you need to apply the algorithm on a scenario (e.g. what is the array on the n-th pass?)

The note types that I used for learning programming are `Image Occlusion`, `Cloze`, and `Basic`.

> Use the [`Syntax Highlighting for Code`](https://ankiweb.net/shared/info/1463041493) add-on to paste code snippets in your flashcards while maintaining syntax highlighting and formatting.
{: .prompt-tip}

> Flashcards are NOT a substitute to actual programming (writing code, compiling, debugging, …). They will not make you better at programming and therefore, you should still work on your programming skills separately by actually programming. 
{: .prompt-warning }

<img src="prog-cards/dst.png" height ="446" width="702" alt = "Anki card on SR flip flop">

<img src="prog-cards/radix.png" height ="446" width="702" alt = "Anki card on Radix Search Trie">

<img src="prog-cards/read-lock.png" height ="446" width="702" alt = "Anki card on Read Lock algorithm">

<img src="prog-cards/theory.png" height ="446" width="702" alt = "Anki card on C++ theory">

<img src="prog-cards/quicksort.png" height ="446" width="702" alt = "Anki card on quicksort recurrence relation">

<img src="prog-cards/output.png" height ="446" width="702" alt = "Anki card where you are asked the output of a C++ program">

### Linux

To learn Linux commands, I relied on the `Basic (type in the answer)` note type. It allows you to type your answer, and when you reveal the card, Anki shows a diff between your input and the true answer.

Another useful note type for learning commands is the `Basic (and reversed card)` where both the front and the back of the card become separate cards. The idea here is that you can test if you can remember the command given an explanation and vice-versa.

<img src="linux/useradd.png" height ="446" width="702" alt = "Anki card on useradd linux command">

<img src="linux/chmod.png" height ="446" width="702" alt = "Anki card on chmod linux command">

### Viva Presentation

I used flashcards to practice answering questions that I may be asked by my examiner for my viva. Here, the goal is not to memorize each answer word-by-word, but rather to remember the gist of the argument.

<img src="viva/cohen.png" height ="446" width="702" alt = "Anki card saying on Cohen d value">

<img src="viva/minified.png" height ="446" width="702" alt = "Anki card on minified function">

<img src="viva/percentile.png" height ="446" width="702" alt = "Anki card on meaning of percentiles">

## Why Digital Flashcards Over Physical Ones

I recommend using digital flashcards over physical ones because:

- Physical flashcards are much easier to lose/damage while digital flashcards can be backed up.
- Physical flashcards are harder to edit over time.
- Physical flashcards are limited in space.
- Physical cards cannot contain multimedia.

## How to Create Good Flashcards

There is an enormous amount of good advice on the internet for creating good flashcards. If you are new to Anki, you should definitely go through the manual first. Here are the guidelines that I use for creating flashcards:

1. Try to use **bullet points** as far as possible when learning theory to ensure that your cards are as simple and concise as possible. This also makes the card easier to skim through.
2. Always include questions that match the format and structure of actual exam questions. Sometimes, you may even have to copy questions directly from past papers to add to Anki. This will ensure that you are always familiar with the type of questions that will appear in the exams.
3. Avoid learning things that you do not fully understand.
4. Use **simple language**. Avoid fancy English words.
5. Flashcards do not need to be perfect on the first attempt. With time, you can make small improvements.
6. **Create your own flashcards** as far as possible instead of downloading random decks from the internet. Creating and editing cards is part of the learning process, since you need to be able to understand something first to be able to create a card for it. Moreover, it is highly unlikely that online decks are catered to your understanding or your teacher's syllabus.
7. Avoid cards that require difficult mental calculations or that assume that you have pen, paper, or calculator available.

## Use of AI

The advent of AI has revolutionized the flashcard generation process. I've used AI extensively to summarize lengthy lecture notes or to generate flashcards.

However, you should be careful when uploading PDFs of lectures to AI models because the PDF can contain text in image format or diagrams in PNG format which the AI cannot parse. Therefore, the AI can miss certain content or even entire slides. Forgetting to revise parts of the lecture can have serious consequences.

My approach to generate flashcards using AI was as follows:

1. Prompt the AI using a master prompt. An example of a prompt that I used was:
    
    ```
    You are an expert in <module-name>. I will provide you with lecture notes, and your task is to create clear, high-quality Anki that will help me understand the material and prepare for exams.
    
    Requirements for the flashcards:
    
    - Use bullet points or numbered lists whenever possible for clarity.
    - Keep the language simple and concise to make facts easy to remember.
    - For each topic, include:
    	- Core concepts (definitions, key facts, examples)
    	- Process or steps (if applicable)
    	- Common pitfalls — highlight typical exam trick questions, misconceptions, or easy-to-miss details.
    - Ensure one fact/question per card to keep cards atomic and focused.
    - Where possible, rephrase complex ideas into easy-to-recall forms.
    - Do not give me plain markdown text and render it. I want to be able to copy paste.
    ```
    
2. Select a few (~5) consecutive slides.
3.  If the selected slides contain images, create your own flashcards from the images instead of relying on the AI.
4. Copy the text from the selected slides and paste it to the AI.
5. For each generated flashcard, read it and verify that its content is correct based on your understanding of the copied slides.
6. If the flashcard passes validation, paste the card to your Anki manually.
7. Repeat steps 2–6 until the entire lecture PDF has been processed.

I treated the AI as a drafting assistant rather than a source of truth.

## Discipline to Learn

Creating flashcards is only the tip of the iceberg. The true work is to actually develop the habit of reviewing the flashcards regularly. This requires motivation and discipline and can be quite challenging but this commitment adds up quickly over the course of a year. To give you an idea of the commitment required, here are some statistics from my final year of CS in 2025:

- I spent around 1 hour daily revising.  
- I reviewed an average of 53 cards and the most reviews I did in a single day was 301.
- 3k flashcards in total
- 19k reviews in total
- ~10s per card

<img src="anki-2025-heatmap.png" height ="446" width="702" alt = "Heatmap for my collection for 2025">
_Heatmap for my collection for 2025_

To develop the daily habit of reviewing flashcards, I did the following:

1. I created a daily reminder on my phone to review Anki cards.
2. I had a dedicated time slot in my schedule for reviewing flashcards. It was either in the morning, around 9 AM, or at night before going to bed.
3. I installed a [heatmap add-on](https://ankiweb.net/shared/info/1771074083) in Anki to keep track of my streaks. Filling each tiny square gave me motivation. 
4. I set a small daily goal for the number of cards to review and gradually increased it.
5. I downloaded the Anki app on my mobile phone, and reviewed cards during commute time.

## Additional Tips

- I recommend having a decent typing speed (> 50 WPM) to make the flashcard creation process easier. Knowing a few Anki keyboard shortcuts is also helpful.
- Always backup your collection regularly as it is very easy to lose months of work.
- Do not use Anki for cramming. Anki is designed for long-term retention, not last-minute revision.
- Anki is NOT a substitute for working out past papers and attending lectures. If a concept does not make sense to you, it will be harder to learn.