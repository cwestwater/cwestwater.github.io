---
layout: single
title: "GPG Keys for Verified GitHub Commits"
# date: YYYY-MM-DD
category: [Other]
excerpt: "How to use a GPG key to get verified commits in GitHub using Visual Studio Code on Windows"
---
## Introduction

When looking at some repositories in GitHub I noticed a green Verified icon next to some commits:

![Verified Commit]({{ site.url }}/assets/images/GitHub-Verified-Commit-01.png)

I started looking into this and finally got it working after finding a missing piece of information. This blog post will detail:

- Installing GPG4Win
- Creating a GPG Key
- Setup Git to use the key
- Setup Visual Studio Code for signed commits

### Assumptions

This is post assumes the following:

- Windows 10 is the OS
- Git for Windows is installed
- Visual Studio Code is installed
- You have a GitHub account

### Installing GPG4Win

GPG4Win is used to create the GPG key that we will use. You can download 
