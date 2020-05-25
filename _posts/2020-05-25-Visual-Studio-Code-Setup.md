---
layout: single
title: "My Visual Studio Code Setup"
date: 2020-05-25
category: [Microsoft]
excerpt: "What Extensions I use, theme, settings, even a bonus item - fonts"
---
## Introduction

As I said in my previous post I recently rebuilt my computer. One of the first things I did was install [Visual Studio Code](https://code.visualstudio.com). I have used [Settings Sync]({{ site.baseurl }}{% post_url 2017-09-10-Sync-Visual-Studio-Code-Settings %}) but I took the opportunity to start from scratch as I had a lot of settings and extensions that were installed on a whim. I took the time to read a few articles and cherry pick tips that were good for me.

### Extensions

These are the extensions I have installed currently:

* [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) to correct my mistypes as I go
* [Log File Highlighter](https://marketplace.visualstudio.com/items?itemName=emilast.LogFileHighlighter) to help format log files while troubleshooting
* [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint) for ensuring good quality Markdown
* [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) for PowerShell work
* [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) to integrate with WSL
* [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync) to sync all these setting to GitHub
* [vscode-icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons) to help identify file types with a glance
* [YAML](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) as I am learning Ansible which relies on YAML

So far these have fulfilled my needs for such task as general PowerShell scripting, writing markdown for blog posts and some Ansible work.

### Terminal Font

This may not be very well known but Microsoft have released an new Open Source font specifically for Terminals called Cascadia Code. I understand it will ship with the release of [Windows Terminal](https://github.com/microsoft/terminal) but you can download it now and use it. It is promoted as:

> This is a fun, new monospaced font that includes programming ligatures and is designed to enhance the modern look and feel of the Windows Terminal.

I like it as it looks clear in terminal applications and it is under active development so who knows what might come from it.

You can download [Cascadia Code here](https://github.com/microsoft/cascadia-code) and to install it simply right click and choose `Install` or `Install for all users`. As simple as any other font.

Now to use it in VS Code open Settings and search for `Editor: Font Family`. To make Cascadia Code the default font just type `'Cascadia Code'` at the front of the setting:

![VS Code Font]({{ site.url }}/assets/images/VSCodeFont.png)

### Other Settings

I have some other settings applied to items such as the Workbench, Editor, extensions, etc. My `settings.json` file looks like this:

~~~ json
{
    // Workbench settings
    "workbench.iconTheme": "vscode-icons",
    "workbench.colorTheme": "Visual Studio Dark",
    // Terminal settings
    "terminal.integrated.shell.windows": "C:\\Program Files\\PowerShell\\7\\pwsh.exe",
    // Editor settings
    "editor.fontFamily": "'Cascadia Code',Consolas, 'Courier New', monospace",
    "editor.renderWhitespace": "all",
    "editor.renderControlCharacters": true,
    "editor.snippetSuggestions": "top",
    "editor.tabCompletion": "on",
    // File settings
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 5000,
    "files.trimTrailingWhitespace": true,
    "files.trimFinalNewlines": true,
    // Code Spell Checker
    "cSpell.language": "en-GB",
    // Setting Sync
    "sync.autoDownload": true,
    "sync.autoUpload": true,
    "sync.gist": "removed",
    "sync.quietSync": true
}
~~~

Note in my settings I define PowerShell 7 as my default terminal by defining the path to the executable. This overrides command prompt.

### Wrap Up

Visual Studio Code is so customisable but I have barely scratched the surface here. I'd love you hear how you setup your VS Code environment.