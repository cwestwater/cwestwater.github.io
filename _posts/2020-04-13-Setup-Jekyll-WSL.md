---
layout: single
title: "Jekyll Setup on WSL Running Ubuntu"
date: 2020-04-13
category: [Blog]
excerpt: "Update on latest way to install Jekyll on WSL running Ubuntu"
---
## Introduction

Back when I started this blog I ran a [series]({{ site.baseurl }}{% post_url 2017-07-17-Blog-Migration-Part-2-Customisation %}) on how I setup the development environment. It seems [Hugo](https://gohugo.io/) is the current hotness for static site generation but I like using [Jekyll](https://jekyllrb.com/) and it's what I know.

This weekend I rebuilt my computer and looking back at the way I setup Jekyll it seems things have changed and the installation instructions are no longer valid.

I thought I'd take the opportunity to document how to setup Jekyll to correctly work with [GitHub Pages](https://pages.github.com/) which is an amazing, free way to host a site. I run Jekyll on an Ubuntu install on my Windows 10 PC using [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about)

### Software Versions

This post was written assuming the following software requirements:

Windows Environment:

* Windows 10 1909
* Windows Subsystem for Linux v1
* Ubuntu 18.04 LTS hosted on the Microsoft Store

Jekyll:

* Jekyll version 3.8.5

### WSL Setup

This is well documented on the [Microsoft Docs page](https://docs.microsoft.com/en-us/windows/wsl/install-win10) but for completeness I will outline the steps.

First we need to install the WSL feature. Open a PowerShell prompt as an Administrator and run:

~~~ powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
~~~

Once complete press `Y` to reboot the computer. Once logged back in it is time to install Ubuntu from the Microsoft Store. Open a PowerShell prompt as an Administrator again:

~~~ powershell
# Move to C:\Temp
Set-Location -Path C:\Temp

# Download Ubuntu 18.04
Invoke-WebRequest -Uri https://aka.ms/wsl-ubuntu-1804 -OutFile Ubuntu1804.appx -UseBasicParsing

# Install Ubuntu 18.04
Add-AppxPackage .\Ubuntu1804.appx
~~~

Ubuntu should now be installed on your computer and appear in the Start Menu. Start the distro and we will have to do some initial setup. You need to add a username and password on the install. This can be different from your Windows credentials.

~~~ bash
Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: cwestwater
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Installation successful!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

cwestwater@DESKTOP-11HR4LU:~$
~~~

The next step is to update the installation with the latest patches/packages:

~~~ bash
cwestwater@DESKTOP-11HR4LU:~$ sudo apt update && sudo apt upgrade -y
~~~

The output above has been truncated. We now have Ubuntu 18.04 LTS installed on Windows 10.

### Jekyll Installation

Next is to install Jekyll. It is a case of broadly following the Jekyll [installation guide](https://jekyllrb.com/docs/installation/ubuntu/) but with one point that needs changed.

First is to install the dependencies:

~~~ bash
sudo apt-get install -y ruby-full build-essential zlib1g-dev
~~~

Once these are installed the next step is to setup environment variables to your `~/.bashrc` file:

~~~ bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
~~~

Next up is to install [Bundler](https://bundler.io/):

~~~ bash
gem install bundler
~~~

Finally we can install Jekyll. Now in the official install guide reference above the command given will install Jekyll v4.0.0. This is incompatible with GitHub pages as referenced in the [GitHub Pages dependencies and versions](https://pages.github.com/versions/). We require Jekyll v3.8.5.

It is simple to specify we want this version:

~~~ bash
gem install jekyll --version 3.8.5
~~~

To check the right version of Jekyll is installed:

~~~ bash
cwestwater@DESKTOP-11HR4LU:~$ jekyll --version
jekyll 3.8.5
~~~

We can also list all installed Gems and compare to the GitHub Pages requried versions by running the command `gem list`:

~~~ bash
cwestwater@CWESTWATER-P500:/mnt/c$ gem list

*** LOCAL GEMS ***

activesupport (6.0.2.2)
addressable (2.7.0)
bigdecimal (default: 1.3.4)
bundler (2.1.4)
cmath (default: 1.0.0)
coffee-script (2.4.1)
coffee-script-source (1.11.1)
colorator (1.1.0)
commonmarker (0.17.13)
concurrent-ruby (1.1.6)
csv (default: 1.0.0)
date (default: 1.0.0)
dbm (default: 1.0.0)
did_you_mean (1.2.0)
dnsruby (1.61.3)
em-websocket (0.5.1)
etc (default: 1.0.0)
ethon (0.12.0)
eventmachine (1.2.7)
execjs (2.7.0)
faraday (1.0.1)
fcntl (default: 1.0.0)
ffi (1.12.2)
fiddle (default: 1.0.0)
fileutils (default: 1.0.2)
forwardable-extended (2.6.0)
gdbm (default: 2.0.0)
gemoji (3.0.1)
github-pages (204)
github-pages-health-check (1.16.1)
html-pipeline (2.12.3)
http_parser.rb (0.6.0)
i18n (1.8.2, 0.9.5)
io-console (default: 0.4.6)
ipaddr (default: 1.2.0)
jekyll (3.8.5)
jekyll-avatar (0.7.0)
jekyll-coffeescript (1.1.1)
jekyll-commonmark (1.3.1)
jekyll-commonmark-ghpages (0.1.6)
jekyll-default-layout (0.1.4)
jekyll-feed (0.13.0)
jekyll-gist (1.5.0)
jekyll-github-metadata (2.13.0)
jekyll-include-cache (0.2.0)
jekyll-mentions (1.5.1)
jekyll-optional-front-matter (0.3.2)
jekyll-paginate (1.1.0)
jekyll-readme-index (0.3.0)
jekyll-redirect-from (0.15.0)
jekyll-relative-links (0.6.1)
jekyll-remote-theme (0.4.1)
jekyll-sass-converter (2.1.0, 1.5.2)
jekyll-seo-tag (2.6.1)
jekyll-sitemap (1.4.0)
jekyll-swiss (1.0.0)
jekyll-theme-architect (0.1.1)
jekyll-theme-cayman (0.1.1)
jekyll-theme-dinky (0.1.1)
jekyll-theme-hacker (0.1.1)
jekyll-theme-leap-day (0.1.1)
jekyll-theme-merlot (0.1.1)
jekyll-theme-midnight (0.1.1)
jekyll-theme-minimal (0.1.1)
jekyll-theme-modernist (0.1.1)
jekyll-theme-primer (0.5.4)
jekyll-theme-slate (0.1.1)
jekyll-theme-tactile (0.1.1)
jekyll-theme-time-machine (0.1.1)
jekyll-titles-from-headings (0.5.3)
jekyll-watch (2.2.1)
jemoji (0.11.1)
json (default: 2.1.0)
kramdown (2.1.0, 1.17.0)
kramdown-parser-gfm (1.1.0)
liquid (4.0.3)
listen (3.2.1)
mercenary (0.3.6)
mini_portile2 (2.4.0)
minima (2.5.1)
minitest (5.14.0, 5.10.3)
multipart-post (2.1.1)
net-telnet (0.1.1)
nokogiri (1.10.9)
octokit (4.18.0)
openssl (default: 2.1.1)
pathutil (0.16.2)
power_assert (0.2.7)
psych (default: 3.0.2)
public_suffix (4.0.3, 3.1.1)
rake (12.3.1)
rb-fsevent (0.10.3)
rb-inotify (0.10.1)
rdoc (default: 6.0.1)
rouge (3.17.0, 3.13.0)
ruby-enum (0.8.0)
rubyzip (2.3.0)
safe_yaml (1.0.5)
sass (3.7.4)
sass-listen (4.0.0)
sassc (2.2.1)
sawyer (0.8.2)
scanf (default: 1.0.0)
sdbm (default: 1.0.0)
stringio (default: 0.0.1)
strscan (default: 1.0.0)
terminal-table (1.8.0)
test-unit (3.2.5)
thread_safe (0.3.6)
typhoeus (1.3.1)
tzinfo (1.2.7)
unicode-display_width (1.7.0)
webrick (default: 1.4.2)
zeitwerk (2.3.0)
zlib (default: 1.0.0)
~~~

We should now have Jekyll and its dependencies installed to publish a blog on GitHub pages.

### Wrap Up

This is a simple set of steps to follow but as someone that does not know Linux very well let alone Ruby it took me a while to figure out why Jekyll was not building my site. The step of using `gem install jekyll --version 3.8.5` is key to success.
