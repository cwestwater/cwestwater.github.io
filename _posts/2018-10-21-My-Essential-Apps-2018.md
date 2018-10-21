---
layout: single
title: "My Essential Apps 2018 Edition"
date: 2018-10-21
category: [Blogtober2018]
excerpt: "A list of the applications/tools I install on all my computers that I rely on"
---
# Introduction

When I setup a new computer there are certain applications I always install. Here is my list of essentials, both free and paid.

## Visual Studio Code - Free

Microsoft Visual Studio Code is one of the first things I install. VS Code is an essential application for me. I use it as my IDE for PowerShell, Markdown editor for these blog posts, Git interface, [log file viewer](https://marketplace.visualstudio.com/items?itemName=emilast.LogFileHighlighter), etc. I love it as through extensions you can setup VS Code to the way you want to work.

If you work in IT you should be using Visual Studio Code.

[Download here](https://code.visualstudio.com/#alt-downloads)

## Snagit - Paid

I think Snagit is one of my most used applications. It is an application to perform screen captures, both static images and videos. There are two parts to the application. The capture tool and the editor.

The capture tool it lets you perform many types of capture. Regions, windows, whole screen, scrolling windows, etc. It also performs video capture. I really like that the tool let you precisely capture what you want. Using the snipping tool is not precise. You always have to edit the capture to remove parts that you don't want. Snagit only captures exactly what you want.

The Editor portion let you work on your capture. You can add text, stamps, highlights, blurs and also crop, cut out, select, etc. The Editor also keeps a gallery of your captures. With the snipping tool once you close it, unless you you have saved the image it is gone forever. Not with Snagit. All your captures are kept until you specifically delete them.

I use Snagit daily to quickly capture things I might need to refer back to, images for documentation, sharing with colleagues, the list goes on. This might sound like a paid promotion from TechSmith but it is a product I pay for out of my own pocket and keep under annual maintenance I find it that valuable. If you are a vExpert you can get a [free license](https://www.techsmith.com/evangelists/mvp)!

[Information/Download here](https://www.techsmith.com/screen-capture.html)

## 7-Zip - Free

7-Zip is a free lightweight (1.37MB download!) zip file utility. It can handle 7z, AR, ARJ, BZIP2, CAB, CHM, CPIO, CramFS, DMG, EXT, FAT, GPT, GZIP, HFS, IHEX, ISO, LZH, LZMA, MBR, MSI, NSIS, NTFS, QCOW2, RAR, RPM, SquashFS, TAR, UDF, UEFI, VDI, VHD, VMDK, WIM, XAR, XY, ZIP and Z files. I have not heard of lots of those file formats! Yes Windows can unpack the common formats such as ZIP and CAB but 7-Zip is more flexible.

I like that you can right click a file and through the context menu Explorer and perform actions such as Open, Extract Here, Extract to a folder named after the file, compress, etc. Such a great application that surpasses what's commercially available.

[Download here](https://www.7-zip.org/download.html)

## Chrome - Free

Many years ago I was a dedicated Firefox user. Since I got my Gmail address in 2004 I have been sucked into the Google ecosystem. Chrome was part of that assimilation. I appreciate that all my settings, bookmarks, extensions, basically your browsing experience is available wherever I want. I like having a consistent experience no matter which computer I am using. One thing I don't let Chrome sync is my password - more on that below.

It may not be memory efficient and its use means Google knows more about me than anyone, but I feel it is the best browser for my needs.

[Download here](https://www.google.com/chrome/)

## LasPass - Free and Paid

As I said above I do not let Chrome handle my passwords. I have been a LastPass user for a number of years now and pay for a subscription. It is common sense to use complex and unique passwords for every place you need to login and LastPass makes that easy. The only password I need to remember is my Master password for the vault.

LastPass generates secure passwords, autofills to the browser, stores secure notes and information, and autofills web forms.

There is a free and paid tier and even though I don't use the premium options I still pay for the subscription. It is my way of showing support for the product I use all the time.

[Information/Download here](https://www.lastpass.com/)

## IrfanView - Free

I first heard about IrfanView back when I had my first PC around 1996. I still think it is the best image viewer available. It is a very small (2.03MB!) and free application that can view [ANY](https://www.irfanview.com/main_formats.htm) image file you can throw at it. There are (optional) plugins available that extend the range of files (and videos) supported even further.

Not only can it view but you can convert, optimize images, perform basic editing, create slideshows, batch process files, scan, even create screensavers.

It's free for non-commercial use and has no crapware bundled. I also like that it has a portable, non installable option.

[Download here](https://www.irfanview.com/)

## Notepad++ - Free

I think pretty much everyone knows about Notepad++. Yes I use VS Code for the majority of my editing, but when I want a fast, simple text editor I use Notepad++ instead of the Windows default Notepad. For a while I used it for everything including PowerShell editing.

Again it's another free and lightweight (4.36MB download) that can be extended using [plugins](http://docs.notepad-plus-plus.org/index.php?title=Plugin_Central). It can handle not just text files but has support for syntax highlighting for languages such as PowerShell, C++, Python, XML, etc. It can also record macros which I have used in the past to save time when working on large file edits.

Go download it and run this command to make it the default text editor:

`reg add "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\notepad.exe" /v "Debugger" /t REG_SZ /d "\"%ProgramFiles(x86)%\Notepad++\notepad++.exe\" -notepadStyleCmdline -z" /f`

[Download here](https://notepad-plus-plus.org/)

## PuTTY - Free

If you need to SSH, Telnet, or even connect via a serial cable PuTTY should be on your list of applications. I use it for SSHing into vCenter, ESXi, my Synology, Pi-Hole, etc. It lets you tweak every possible option for connecting to a device.

I found a great article on how to setup PuTTY for Windows. I follow the steps in the article when I setup PuTTY on a new machine. Find the blog post [here](http://dag.wiee.rs/blog/content/improving-putty-settings-on-windows).

[Download here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

## Wrap Up

I am sure everyone has their list of applications they always install. What have I missed? Please let me know what you recommend.