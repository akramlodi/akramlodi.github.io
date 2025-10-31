+++
authors = ["Mohammed Akram"]
title = "Learning with LLM's"
description = "This is a post about my experience learning with LLM's for my university"
date = 2025-04-25

[taxonomies]
tags = ["Learn", "exams", "university"]
[extra]
banner = ""
toc = true
toc_inline = true
toc_ordered = true
featured = true
disclaimer = """
- No neurons were harmed in the making of this study method (except maybe a few around 3 AM). 
- This post is not a replacement for good sleep, consistent study habits, or actual human willpower. 
- Side effects may include overconfidence, caffeine addiction, and developing an emotional attachment to your AI. Use responsibly.
"""
[extra.comments]
# Long thread with image
#
# host = "mastodon.social"
# user = "brownpau"
# id = "104529877688537579"
#
# Thread with multiple images per post
#
# host = "mastodon.blaede.family"
# user = "cassidy"
# id = "112774854109302186"
#
# Thread with preview cards
# host = "mastodon.blaede.family"
# user = "cassidy"
# id = "110669429936617026"
#
# Post on GoToSocial
#
# host = "alpha.polymaths.social"
# user = "orbitalmartian"
# id = "01J7ETKJ19FGBDQGS1ZWZ3KEPP"
#
# Post on Sharkey
#
# host = "is-a.wyvern.rip"
# user = "volpeon"
# id = "9qy755nsnu2c0hbc"
host = ""
user = ""
id = ""
+++

<!-- {% alert(tip=true) %}
Recommended banner dimensions are 2:1 aspect ratio and 1920x960 resolution.  
Other sizes will also work, but will be cut off at the bottom/won't be high enough.
{% end %} -->


<!-- <figure> -->

<!-- ![The Office](the-office.webp)
<figcaption>The Office where Stanley works, it has yellow floor and beige walls</figcaption>
</figure> -->

## The problem:
Let's face it, it's a pretty fast-paced world, and I'm not really a fan of sitting down and browsing Google or YouTube to learn about something. Too many ads, too many "This site uses cookies..." pop-ups, and too much fluff. I have an exam in a few hours and don't have the time nor patience to sit through all of that...

## My solution

So, being the considerate student that I am, instead of studying consistently and managing my mental health, I decided to use AI to study the night before my exams.

What better way to test if learning via AI works than giving it to a caffeine-addicted student the night before his test? Which is exactly what I did.

### Here's how I do it
Take a look at the syllabus and gather all your materials into one place (your notes, PPTs, PDFs, etc.).

Ask the AI to explain each of the topics in your syllabus in simple words, considering you’re a beginner on the subject:

```prompt
"Please explain each of the above topics in simple words, I am a complete beginner on this topic, and provide real-world examples wherever necessary."

"Give me a general abstraction on each of these topics, and explain like I'm 5."
```

Start dumping all of this (my study material like PPTs and notes) into ChatGPT or NotebookLM, and then ask it to give a general overview on each of these topics in simple words.

Once you get a good understanding of the content, you can start going into details by prompting:

```prompt
"Please go into details for [topic_name]"
```
If you think it's too wordy for you, ask it to use simple words/language.
If previous year papers are available, finally add them in and ask AI for the answers. This helps to set the stage up and gives you a good understanding of the content. Provided your professor sets the paper in a similar pattern, this is a huge help.

I repeat this process for each of the topics, and I'm done. This usually takes me a few hours (2-3) depending on the content.

Note
Well, the above method only works for my uni exams because they are theory-intensive. When I have to learn something general, like networks or algorithms, I use the following prompt and ask follow-up questions:

```prompt
"Please explain [topic] in simple words. I am a complete beginner. Start with an abstraction of the topic and then go into details. Please provide real-world applications and examples."
```

{% alert(tip=true) %}
If your exam involves coding, you could also ask it for example questions, which helps in practicing and getting a good understanding of the subject.
{% end %}


### Final Result
CGPA: 9.67/10
(Just to reiterate my stance, am I narcissistic? Probably.)

That’s pretty much it. Considering that LLMs (Large Language Models) are trained on internet data, I like to use simple language when prompting them. Also, there hasn't been any formal study into using the word "Please" when prompting LLMs to give you better results, but I do think that when the AI takes over, considering the fact that I was kind to it despite slaving it for hours every day, it might not torture me.

{% alert(tip=true) %}
Remember: be kind to your AI. You never know what might come back to haunt you after exams. 😉
{% end %}
