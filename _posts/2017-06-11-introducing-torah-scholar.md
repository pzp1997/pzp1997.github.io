---
title: "Introducing Torah Scholar for Alexa"
---

This past week, I published my first Amazon Alexa skill. The skill is called Torah Scholar, and it lets you ask Alexa for verses and commentary from the Tanakh (Old Testament).

The code for the skill is written in Python using [Flask Ask](https://alexatutorial.com/flask-ask/), a great library for making Alexa skills. I also use the Sefaria API as the source for the text itself. The code can be found on my GitHub in the [alexa-torah-scholar repo](https://github.com/pzp1997/alexa-torah-scholar).

The project has been a few months in the making. Over winter break, I became interested in making Alexa skills and got the idea to make an Alexa client for Sefaria. (For reference, I interned as a software engineer at Sefaria last summer.) By the end of winter break, I had developed most of the skill's basic functionality. I was planning on publishing it then, but once the spring semester started it got pushed towards the end of my todo list. Luckily, I was able to find some time about two weeks ago to finish it. I added support for commentaries and verse ranges, polished it up, and submitted it for Amazon certification (which it passed!).

You can install the skill from the [Alexa Skills store](https://www.amazon.com/dp/B0723D84GS/) or by simply telling your Alexa to "Enable the Torah Scholar skill."
