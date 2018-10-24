---
layout: single
title: "Super Meetup Presentation"
# date: YYYY-MM-DD
category: [Blogtober2018]
excerpt: "My presentation at the October SQL/Azure/PowerShell Super Meetup"
---
# Introduction

On the 24th October 2018 the Glasgow Azure, SQL, and Scottish PowerShell & DevOps User Groups had a joint Super Meetup.

Thanks to [Sarah Lean](https://twitter.com/TechieLass) of the Azure group I was asked to do a 10 minute lightning talk on a topic of my choice. I decided to present a session on 'Visual Studio Code for PowerShell'.

## Why Visual Studio Code?

A big reason to switch to Visual Studio Code is that Microsoft are not developing PowerShell 5.1 any more, so the ISE is not stagnant. VS Code is being actively developed and anyone can contribute to the development through GitHub.

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

~~~ posh
"terminal.integrated.shell.windows": "C:\\Program Files\\PowerShell\\6\\pwsh.exe",
~~~

