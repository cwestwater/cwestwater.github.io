---
layout: single
title: "vSphere DSC - Part 2"
# date: YYYY-MM-DD
category: [VMware]
excerpt: "This is Part 2 of a series on vSphere DSC. We will cover some simple vSphere DSC examples"
---
## Introduction

This is Part 2 of a series of posts on vSphere DSC. If you have not read it go back and read [vSphere DSC - Part 1]({{ site.baseurl }}{% post_url 2019-08-26-vSphereDSC-Part-1 %}).

We now have vSphere DSC installed and the pre-requisites such as PowerCLI. We can now get to the good stuff.

## Structure of the the Configuration

The main DSC construct is a Configuration. This is a code block which defines a few things:

1. A keyword `Configuration` followed by the name of the configuration
2. A Node block which defines the servers we want to configure
3. The DSC Resource you are using
4. The properties you are configuring (in key value pairs)

An empty configuration could look as so:

~~~ posh
Configuration ConfigName {

    Node ServerName
    {
        File ResourceName
        {
            Type = File
            ...
        }
    }
}
~~~

What initially threw me when using vSphere DSC was the part about defining the server you want to configure (above is `Node ServerName`). Usually you put the server name such as `DC1.corp.contoso.com` so I was putting in `esxi1.corp.contoso.com` but vSphere DSC is different.

You always define the node as `localhost`. This is simply due to the fact the DSC module is using PowerCLI that is installed on **your machine** under the hood to make the changes on the remote vCenter/ESXi host. So you are always actually pointing at the machine you are running the Configuration on to make the changes.



## ESXi Host Examples

## vCenter Examples

## Wrap Up
