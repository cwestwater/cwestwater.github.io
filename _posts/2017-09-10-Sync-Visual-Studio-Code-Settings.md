---
layout: single
title: "Sync Visual Studio Code Between Computers"
date: 2017-09-10
category: [Microsoft]
excerpt: "Do you run copies of Visual Studio Code across multiple computers? You can sync the settings between them with an extension"
---
I have [Visual Studio Code](https://code.visualstudio.com/) setup on both my personal and work computers. Often I download an extension or made a config change on one copy then had to remember to do the same on the other installs. Not ideal. However these is a way to sync all of the settings including installed extensions between copies using an extension. Here is how you do it.

The extension in question is called [Settings Sync](https://github.com/shanalikhan/code-settings-sync) which can be downloaded from the [Marketplace](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync) or directly in VS Code as normal. From the website the key features are:

1. Use your GitHub account token and Gist.
2. Can create Anonymous Gist without using your GitHub account token.
3. Easy to Upload and Download on one click.
4. Show a summary page at the end with details about config and extensions effected.
5. Auto Download Latest Settings on Startup.
6. Auto upload Settings on file change.
7. Share the Gist with other users and let them download your settings.
8. Supports GitHub Enterprise

It syncs:

1. Settings File
2. Keybinding File
3. Launch File
4. Snippets Folder
5. VSCode Extensions Settings
6. Workspaces

It is pretty easy to setup. The extension uses a GitHub Gist and Key to store your settings on GitHub and each copy syncs to and from that.

# Setup the key
In Github go to Settings...Personal Access Tokens...Generate New Token

![Generate Token]({{ site.url }}/assets/images/gist-setup-01.png)

Give the Token a description and then make sure under Select scopes check gist. Click Generate token

![Generate Token]({{ site.url }}/assets/images/gist-setup-02.png)

This will then generate a token. Make sure you copy this code as you will never see it again.

![Token]({{ site.url }}/assets/images/gist-setup-03.png)

# Upload initial settings
Once the extension is installed press F1 to open the Command Palette. Type in Sync and select the option Sync : Update / Upload Settings:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-01.png)

Enter the Token generated above:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-02.png)

The extension will then upload all your settings to a Gist. This Gist is secret, only accessible to you. The Gist ID needs to be copied to download the settings on another computer. Take a note of it:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-03.png)

You can view this Gist and the settings uploaded by going to https://gist.github.com/{github_username}//{gist id}:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-04.png)

# Download settings on another computer
Once the extension is installed on the computer, press F1 to open the Command Palette. Type in Sync and select the option Sync : Download Settings:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-05.png)

Enter the Gist Id:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-06.png)

Once the download is completed a sync summary is displayed:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-07.png)

# Summary page
By default the summary page is displayed after a sync. This can be turned off by pressing F1 to open the Command Palette. Type Sync and select the option Sync: Advanced Options:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-08.png)

Then in the Advanced options select Sync : Toggle Show Summary Page On Upload / Download:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-09.png)

# Automatically sync settings
When a change is made to you settings by default you need to manually upload and download. You can change an advanced option to enable upload and download automatically when settings are changed. This can be turned on by pressing F1 to open the Command Palette. Type Sync and select the option Sync: Advanced Options:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-08.png)

Then in the Advanced options select Sync : Toggle Auto-Upload On Settings Change:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-10.png)

Once enabled you get a confirmation:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-11.png)

Then you need to enable Auto Download. This can be turned on by pressing F1 to open the Command Palette. Type Sync and select the option Sync: Advanced Options as above. Then select Sync : Toggle Auto-Download On Startup:

![Sync Setup]({{ site.url }}/assets/images/vscode-setup-12.png)

That's it. The copy of VS Code will automatically sync with the settings Gist. Just do this on all your copies of VS Code.

# Wrap Up
There are plenty of other options available with this extension. See the [GitHub Page](https://github.com/shanalikhan/code-settings-sync) from Shan which explains everything. I highly recommend you use the extension if you are using multiple computers.