---
layout: single
title: "Super Meetup Presentation"
date: 2018-10-25
category: [Blogtober2018]
excerpt: "My presentation at the October SQL/Azure/PowerShell Super Meetup"
---
# Introduction

On the 24th October 2018 the Glasgow Azure, SQL, and Scottish PowerShell & DevOps User Groups had a joint Super Meetup.

Thanks to [Sarah Lean](https://twitter.com/TechieLass) of the Azure group I was asked to do a 10 minute lightning talk on a topic of my choice. I decided to present a session on 'Visual Studio Code for PowerShell'.

## Why Visual Studio Code?

A big reason to switch to Visual Studio Code is that Microsoft are not developing PowerShell 5.1 any more, so the ISE is stagnant. VS Code is being actively developed and anyone can contribute to the development through GitHub.

As Microsoft are now focusing on the cross platform [PowerShell Core](https://github.com/PowerShell/PowerShell) you need to use VS Code to have an IDE.

VS Code is multi-platform and is frankly a great IDE!

## Installation

Download [VS Code here](https://code.visualstudio.com). It is available for Windows, Mac and Linux. A portable .zip option is available for download too.

A recent change is there is a download available (default) to install under the User context. This means no admin rights are needed to install.

## PowerShell Extension

A must for using VS Code with PowerShell is installing the [PowerShell extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). This adds the following features to VS Code:

* Syntax highlighting
* Code snippets
* IntelliSense for cmdlets and more
* Rule-based analysis provided by PowerShell Script Analyzer
* Go to Definition of cmdlets and variables
* Find References of cmdlets and variables
* Document and workspace symbol discovery
* Run selected selection of PowerShell code using F8
* Launch online help for the symbol under the cursor using Ctrl+F1
* Local script debugging and basic interactive console support

PowerShell 3, 4, 5, 5.1, and Core 6 is supported in VS Code. There is an integrated terminal so you can do everything in VS Code.

## Integrated Terminal

The terminal allows you to run your PowerShell code in VS Code. You can edit the settings.json file to default to either 5.1 or 6 (assuming 6 is installed). The default is 5.1, so to change it to Core add the following line to your settings file:

~~~ JSON
"terminal.integrated.shell.windows": "C:\\Program Files\\PowerShell\\6\\pwsh.exe",
~~~

You don't just have to default the terminal to PowerShell. You can set applications like the Windows System for Linux, Git Bash, even the good old command prompt.

There is an extension available that lets you choose which command line interface you want to launch in the terminal. This is called [Shell Launcher](https://marketplace.visualstudio.com/items?itemName=Tyriar.shell-launcher). It's easy to setup. Install the Extension and in settings.json add the following:

~~~ JSON
"shellLauncher.shells.windows": [
        {
            "shell": "C:\\Windows\\System32\\cmd.exe",
            "label": "cmd"
        },
        {
            "shell": "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
            "label": "PowerShell 5.1"
        },
        {
            "shell": "C:\\Program Files\\PowerShell\\6\\pwsh.exe",
            "label": "PowerShell Core"
        }
    ]
~~~

This adds the command prompt, PowerShell 5.1, and PowerShell Core to the extension. Now if you want to launch any particular shell press F1 and search for `Shell Launcher: Launch` and you will have a drop down showing the options configured.

## Top Extensions

I have a few recommendations for Extensions to install.

* [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) by Microsoft
* [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync) by Shan Khan. Allows all setting sand extensions to be synced across installs
* [Log File Highlighter](https://marketplace.visualstudio.com/items?itemName=emilast.LogFileHighlighter) by Emil Astrom. Adds colour highlighting to log files
* [Markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint) by David Anson. Compares your markdown to a set of best practice 'rules' for markdown standards
* [vscode-icons](https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons) by Robert Huertas. Adds nice icons to the file types in Explorer
* [Shell Launcher](https://marketplace.visualstudio.com/items?itemName=Tyriar.shell-launcher) by Daniel Imms

## Wrap Up

Thanks to everyone that attended the Meetup and thanks to Sarah for asking me to present. The event was recorded and is available for viewing [here](https://www.youtube.com/watch?v=QS_gppC5UWQ). You can grab the my PowerPoint slides [here]({{ site.url }}/assets/pptx/VisualStudioCode.pptx).