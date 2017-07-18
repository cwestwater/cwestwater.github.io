---
layout: single
title: "Blog Migration Part 3 - Workflow"
date: 2017-07-18
---
## Introduction
I was using Blogger as my platform for hosting this blog. It was ok but things like adding code was a pain to format and get right. I never liked the templates that were available and the editor was pretty basic. I had heard about using GitHub for hosting your website and as I am keen to learn more about GitHub, it seemed like a good idea to see if I could migrate the blog to GitHub Pages. Turns out doing this I learn a lot of new things such as GitHub, Markdown, Jekyll, Visual Studio Code and a little Linux.

This is Part 3 of a three part series:
1. [Part 1 - Setup]({{ site.baseurl }}{% post_url 2017-07-16-Blog-Migration-Part-1-Setup %})
2. [Part 2 - Customisation]({{ site.baseurl }}{% post_url 2017-07-17-Blog-Migration-Part-2-Customisation %})
3. Part 3 - Workflow

In this post I will show the workflow in creating a new blog post. I will also provide some links I found helpful in this process.

You can view this sites raw Jekyll files on GitHub at [cwestwater/cwestwater.github.io](https://github.com/cwestwater/cwestwater.github.io)

## Workflow
So this is my workflow when create a new blog post.

1. Launch Bash on Ubuntu for Windows
2. In bash run the commands browse to your local repository directory and run Jekyll using the command **jekyll serve --watch --force_polling --drafts**. Note the --drafts option
3. Create the post using your favourite editor. I like [Visual Studio Code](https://code.visualstudio.com/) as it has a [Markdown extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Theme-MarkdownKit) which helps when writing. Make sure you put in your YAML front end code at the start of the post. Save the .md file in the _drafts folder
4. When you save the file, Jekyll will see the new file in the drafts folder and regenerate the site. Browse to [http://127.0.0.1:400](http://127.0.0.1:4000) to view the post on the site.
5. Once happy with the post move the file from _drafts to _posts
6. I stop and then start Jekyll again without the --drafts option. Make sure the post is shown
7. Commit the changes to the local repository using GitHub Desktop
8. Push the local copy to GitHub using GitHub desktop
9. Keep hitting F5 on the internet blog site until GutHub refreshes the site

It's actually a simple process once you have done it a couple of times. VS Code has a Git Client built in which I will migrate too, but I want to get comfortable first with GitHub.

## Google Analytics
One thing I missed from the Blogger platform was the basic stats about the site. Was anyone actually reading this content? It's pretty easy to use [Google Analytics](https://analytics.google.com) to replace this, and it's more powerful data. Sign up your domain URL in Analytics and get your Tracking ID. Open _config.yml and browse to the # Analytics section. Change the provider: to "google" and put your tracking_id: in.
~~~ yml
# Analytics
analytics:
  provider               : "google"
  google:
    tracking_id          : "Tracking ID"
~~~

## Branching
Another benefit to using Github for the site is when making changes to the structure or configuration. Instead of potentially breaking your production master branch, simply create a Test branch. Make your changes there, make sure it works, then merge back into Master.

## Links
Here are some links I found useful while going through this process
* [Minimal Mistakes Quick-Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
* [Brian Bunke's post](http://www.brianbunke.com/blog/2017/05/24/jekyll-win10/) on setting up Jekyll on Windows 10
* [GitHub Pages](https://pages.github.com/)
* [Jekyll Website](https://jekyllrb.com/)
* [GitHub markdown cheat sheet](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)
* A page with lots of pre built [Jekyll Themes](http://jekyllthemes.org/)

## Conclusion
This has been a fun process for me. I learnt a lot and I feel my blog looks so much better now and is infinitely customisable. If you are thinking of starting a site or want to migrate, GitHub pages with Jekyll is definitely worth considering.