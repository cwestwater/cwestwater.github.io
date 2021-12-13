---
layout: single
classes: wide
title: "Change $FormatEnumerationLimit"
date: 2021-12-13
category: [Microsoft]
excerpt: "How to show all entries returned when using Format-List in PowerShell"
---
## Introduction

When I'm working in the PowerShell Console by default when using Format-List you see the data returned cut off. This can be annoying! In this post I will show you how to use `$FormatEnumerationLimit` to show all the data in the list.

This post was written using PowerShell 7.2 on a Windows 10 VM in a Windows Server 2022 Domain.

### Default Behaviour

For this post I could have used many example cmdlets with `Format-List` but I will use this one:

```posh
Get-ADUser -Identity Administrator -Properties * | Format-List -Property Name,MemberOf
```

What I am doing is seeing all the groups the Administrator user is a member of. If we run this we see:

```posh
PS C:\> Get-ADUser -Identity administrator -Properties * | Format-List -Property Name,MemberOf

Name     : Administrator
MemberOf : {CN=Group Policy Creator Owners,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Domain
           Admins,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Enterprise Admins,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Schema
           Admins,CN=Users,DC=ad,DC=vgemba,DC=com…}
```

You can see at the end of the `MemberOf` the `...` - this means the data has been cut off. It seems we are only seeing 4 entries. How many groups is Administrator actually a member of?

```posh
PS C:\> Get-ADPrincipalGroupMembership -Identity Administrator | Measure-Object | Select-Object -ExpandProperty Count
6
```

So this confirms we are not seeing all the groups returned from `Format-List`.

### $FormatEnumerationLimit

I was actually working on something else when this cut off was annoying me so I went down a rabbit hole that led me to [$FormatEnumerationLimit](https://docs.microsoft.com/en-gb/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7.2#formatenumerationlimit). From the documentation:

> Determines how many enumerated items are included in a display. This variable doesn't affect the underlying objects, only the display. When the value of $FormatEnumerationLimit is fewer than the number of enumerated items, PowerShell adds an ellipsis (...) to indicate items not shown. Default value: 4

From the documentation we can change that limit to any value we want.

### Change $FormatEnumerationLimit

Here I will change the limit to 6 and try to list the Groups again:

```posh
PS C:\> $FormatEnumerationLimit = 6
PS C:\> Get-ADUser -Identity Administrator -Properties * | Format-List -Property Name,MemberOf

Name     : Administrator
MemberOf : {CN=Group Policy Creator Owners,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Domain
           Admins,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Enterprise Admins,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Schema
           Admins,CN=Users,DC=ad,DC=vgemba,DC=com, CN=Administrators,CN=Builtin,DC=ad,DC=vgemba,DC=com}
```

This time all the groups are displayed. Of course we know there are 6 groups, so changed the limit to 6. What if we didn't know how many groups where in `MemberOf`? You could set the limit to a high number such as:

```posh
PS C:\> $FormatEnumerationLimit = 1000
```

but I don't really like that as a solution. What is not detailed in the Microsoft [documentation](https://docs.microsoft.com/en-gb/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7.2#formatenumerationlimit)  is this:

```posh
PS C:\> $FormatEnumerationLimit = -1
```

Using `-1` means remove the limit, so all output is returned to the screen.

## Wrap Up

This is a handy hint when working on the console and using `Format-Table`. Please note however changing the limit only lasts as long as the console is open. Once you open a new console the limit is back to 4.

Remember this post when you are viewing output in the PowerShell console.