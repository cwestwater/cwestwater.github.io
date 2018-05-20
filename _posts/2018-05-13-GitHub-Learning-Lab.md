---
layout: single
title: "GitHub Learning Lab"
date: 2018-05-13
category: [GitHub]
excerpt: "GitHub release a tool for learning GitHub."
---
# Introduction

At a VMUG last year during a presentation by [Chris Wahl](https://twitter.com/ChrisWahl) he recommended that all ops people like me learn a Distributed Version Control System such as GitHub. I use GitHub for my blog and storing some files, and still had not really scratched the surface of it.

Last month GitHub released a tool called [GitHub Learning Lab](https://blog.github.com/2018-04-19-introducing-github-learning-lab/) that is basically an app that starts a bot that leads you through some training on the use of GitHub.

## Lessons

So far there are five lessons available:

* Introduction to GitHub
* Communicating using Markdown
* GitHub Pages
* Moving your project to GitHub
* Managing merge conflicts

In the Introduction to GitHub lesson you learn about:

![Introduction to GitHub]({{ site.url }}/assets/images/Intro-To-GitHub.png)

You can see you learn the basics of the main things in Git/GitHub like issues, branching, pull requests, etc.

## How it works

Once you start the lab, the bot creates a new repository for you and immediately creates an issue. The bot interacts with you by creating issues and commenting on pull requests. You can see an example of this here:

![Introduction to GitHub Issue]({{ site.url }}/assets/images/Intro-To-GitHub-Assign.png)

You can see there is an issue created called `Getting Started with GitHub` and the bot has commented explaining what you are going to learn and what it wants you to do to progress. In the example above it begins explaining what GitHub is and how to use issues. Notice you can expand sections to get more information and there are even videos available too.

The very first thing I needed to do was assign the issue to myself. Once I did this within a few seconds the bot recognised this and added a new comment to the issue:

![Introduction to GitHub Assign]({{ site.url }}/assets/images/Intro-To-GitHub-Issue.png)

You then follow the course through to completion.

## What I liked and learned

I had been using GitHub pages for my [blog platform]({{ site.baseurl }}{% post_url 2017-07-16-Blog-Migration-Part-1-Setup %}) for a while now and thought I 'knew' GitHub but even on the introduction course I learnt new things. For example on commits I was doing something like:

Subject: New blog post / Description: Scottish VMUG April 2018 post

But in the course I learnt the commit rules:

> Rules to live by for commit messages:
> * Don’t end your commit message with a period.
> * Keep your commit messages to 50 characters or less. Add extra detail in the extended description window if necessary. This is located just below the subject line.
> * Use active voice. For example, "add" instead of "added" and "merge" instead of "merged".
> * Think of your commit as expressing intent to introduce a change.

I was treating my commits as just a stepping stone to publishing posts when in fact they should almost be a story to explain what is happening and why. Maybe this isn't so important when simply adding blog posts but I firmly believe in getting the basics right and practicing them.

I liked how the bot used the system that it was trying to teach you. It was a live repository being used and you are performing steps 'in the real world'. Also as it's only me working on my repositories I didn't have anyone doing comments, issuing pull request, etc. but the bot was being a second person working with me.

## Conclusion

I would highly recommend using GitHub Learning Labs to gain useful, practical knowledge of GitHub. It provides a nice interactive way of leading you through lessons that are quick but well thought out. I also see there are more lessons coming (coming soon is `Contributing to open source`) so hopefully this will be a set of lessons that is always relevant and up to date. I like to learn by doing instead of watching videos so this is great for me.

[Access GitHub Learning Labs here](https://lab.github.com/)
