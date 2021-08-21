---
layout: single
classes: wide
title: "My Top 10 Sessions to see at VMworld 2021"
# date: YYYY-MM-DD
category: [VMware]
excerpt: "There are hundreds of sessions available at VMworld but what do I see as the Top 10 must see "
---
## Introduction

I recently posted about [VMworld 2021]({{ site.baseurl }}{% post_url 2021-07-26-VMworld-2021 %}) and in that post I listed out the sessions I really wanted to see. Since then more sessions have been added so the list has changed slightly. In this post I am going to expand on those sessions and why I think they are worth attending or watching.

### Build and Publish a PowerShell Module to the PowerShell Gallery [CODE2756]

Excerpt:

> Have you ever created a PowerShell module and needed to share it and didn't know how? The PowerShell gallery is a great place to do this! Join this session and learn how to build and publish a PowerShell module to the PowerShell gallery and allow your contribution to be shared with the VMware Community!

Speaker:

- [David Stamen](https://twitter.com/davidstamen)

My PowerShell/PowerCLI is 100% written for me to run. It's hacky, uncommented and not written to best practices. I need to make more of an effort to write them as modules which are much easier to run for people that are using the code.  The end goal for some people will be to make the module available for anyone to use. I'm hoping to learn some good habits to use in my code to make it easier for anyone to use.

### Achieving Happiness: The Quest for Something New [IC1484]

Excerpt:

> “Achieving Happiness: Building Your Brand and Your Career” has been a staple of VMworld and VMware User Group (VMUG) conferences the past few years. However, the changes in how we live and work due to the pandemic have significantly changed what is under our control and what is not. Therefore, we can influence our personal happiness, career trajectory, and brand in different ways than we did before. A critical component to our happiness is adding variety and new opportunities into our lives. This talk will cover how to evolve your brand and career in a way that is relevant for 2021 through alignment to your individual happiness.

Speakers:

- [Amanda Blevins](https://twitter.com/AmandaBlev)
- [Steve Athanas](https://twitter.com/steveathanas)

The original session "Achieving Happiness: Building Your Brand and Your Career" hosted by Amanda has been probably been my favourite session as any conference. Seriously go back and look at the [original session](https://www.vmware.com/vmworld/en/video-library/video-landing.html?sessionid=1589294822914001xYJx). The session really helped me realise my job is not my career and how both can influence how I feel. In a sea of technical content go watch this. It's a no brainer for me to watch the 2021 edition.

### Always Available Infrastructure in vSphere - Design Studio [UX2501]

Excerpt:

> Have you ever struggled when vCenter is unavailable and critical operations like Provisioning, Distributed Resource Scheduler (DRS), and High Availability (HA), tightly coupled with the vCenter availability, are not functioning? We are working on a solution that will make your infrastructure independent from vCenter failure to ensure that there will be no impact on your ability to provision, manage and monitor it. Let's find out together the best possible ways to address your needs.

Speaker:

- [Darina Stoyanova-Mincheva](https://www.linkedin.com/in/darina-stoyanova-8b329243)

I have experienced a time when vCenter was unavailable. That was not a fun day. My theory for this session is the move to break up vCenter into a collection of services/vms that can run across multiple hosts. I think this is super interesting - vCenter microservices. I'm hoping this is what this session covers.

### 10 Things You Need to Know About Project Monterey [MCL1833]

Excerpt:

> Project Monterey was announced in the VMworld 2020 keynote. There has been tremendous work done since then. This session details how Project Monterey and SmartNICs will redefine the data center with decoupled control and data planes for management, networking, storage and security—for VMware ESXi hosts and for bare-metal systems. We will discuss 10 things you need to know about Project Monterey to help accelerate your business. Find out how it helps to increase performance, security and manageability for the entire spectrum of VMware vSphere and bare-metal infrastructure management. We will cover and demo the overall architecture and use cases.

Speakers:

- [Sudhanshu Jain](https://twitter.com/sudhanshu_jain)
- [Niels Hagoort](https://twitter.com/NHagoort)

I'm really excited about the possibilities from SmartNICs. A simple example is being able to offload firewall compute traffic to a NIC which is closer to the workload and free up the main CPU cycles for VMs. Project Monterey is VMware's foray into SmartNICs which could be revolutionary for workloads and optimum workload placement. Increased security, disaggregation, performance are some of the benefits I see of Project Monterey. I want to know more.

### Automation Showdown: Imperative vs Declarative [CODE2786]

Excerpt:

> The automation landscape has always been a source of rapid innovation. Historically, the languages, whether it's Perl, Python, vRealize Orchestrator JavaScript, or PowerCLI, may have changed, but the imperative, step-by-step workflows you've learned and know have not. However, a new challenger has appeared. Declarative workflows upended the usual processes and even the languages all in the name of infrastructure as code. Human readable, plain text files can be interpreted by products like HashiCorp Terraform and RedHat Ansible to do the heavy lifting of the imperative process. The key is knowing when, how, and where to use each method within your VMware environment. Join Luc and Kyle for this session where they will discuss these different styles of automation, complete with practical examples that you can use in your own environment!

Speakers:

- [Luc Dekens](https://twitter.com/LucD22)
- [Kyle Ruddy](https://twitter.com/kmruddy)

Infrastructure as code is pushed as **the** way to create infrastructure, but there are two ways. The 'traditional' way where you code out each step of the process to get your end sate, or the 'new' way where you declare the end state and the tool figures out how to get there. This session looks like a good primer on each method, and should give you some tips on how to start your IaC journey.

### Deep Dive: Treating Security Like a Product [APP1436]

Excerpt:

> In software organizations, security is often treated as an afterthought instead of being integrated throughout the entire SDLC. This is frequently because development teams and security personnel alike see security as an external requirement instead of as an added, shared and necessary benefit of building and deploying resilient and reliable software. Hannah Hunt, chief of product at the Army Software Factory, and Alex Barbato, VMware solutions architect, will explain from their experiences in multiple organizations how security can be treated like a product to create stronger security outcomes and speed up the time to delivery. This session will leave you with a better understanding of the benefits of elevating the importance of treating security as a product that improves the delivery and reliability of software applications.

Speakers:

- [Alex Barbato](https://www.linkedin.com/in/alex-barbato-33ab7a66/)
- [Hannah Hunt](https://www.linkedin.com/in/hannah-feldman-hunt-b8646a97/)

No matter what role you have in IT, security should be something you think about (and be paranoid about!). Unfortunately it's sometimes an afterthought. Even for us infrastructure folks doing IaC we should be thinking about security (are you sure you have not committed any credentials to source control?). I'm hoping to gather more information about how we should be treating security from the ground up.

### DevSecOps for Infrastructure – Adopting DevOps Practices for vSphere Admins [APP1842]

Excerpt:

> DevOps, SecOps, GitOps, infrastructure as code, continuous this and that—are they all just abstract buzzwords? Is it just a way to keep a handful full-stack developers happy, or are there lessons the rest of us can learn and put into practice? If you don't know your declarative from imperative, or the difference between mutability and idempotency, then this is the session for you. Gain strategic and practical insights on how you can apply the principles of DevSecOps and improve the reliability, security and efficiency of your infrastructure.

Speakers:

- [Lefteris Marakas](https://www.linkedin.com/in/lefterismarakas/)
- [Sam McGeown](https://twitter.com/sammcgeown)

I have some opions on DevOps which I wont; share on this post, but looking at the trends in IT Infrastructure people more and more need to be thinking like a developer. I personally find it hard to visualize how some DevOps principles can apply to me while setting up a vSphere cluster, I know the core principles of DevOps so I'm hoping for a more infrastructure slant on it from Sam and Lefteris.

### Introduction to Kubernetes for the vSphere Admin [APP2183]

Excerpt:

> Are your development teams asking for environments to run containers or Kubernetes? Do you want to up-level your profile to a platform or DevOps engineer? This session will walk through what Kubernetes is and its architecture, and how it combines infrastructure primitives (compute, network and storage) to build API-driven application deployments. Find out how to leverage your existing VMware vSphere or software-defined data center (SDDC) environments to build Kubernetes clusters.

Speaker:

- [Boskey Savla](https://www.linkedin.com/in/boskey/)

![Kubernetes Meme]({{ site.url }}/assets/images/kubernetes-meme.png)

I listen to a lot of podcasts and read a lot of blogs. At this point you can't go anywhere without hearing about Kubernetes and how it is some magical pixie dust to run your application workloads. I know it's something I need to know more about as I don't think it can be avoided until I retire in 25 years, so I better start expanding my knowledge on it. Another sessions where I hope it's aimed towards us vSphere Administrators and not the 'rock star' developers.

### Live Coding: Terraforming Your vSphere Environment [CODE2755]

Excerpt:

> Infrastructure as code is the process of managing infrastructure in a file or a set of files rather than manually configuring resources in a user interface. This session is going to take a live look at how to make the process of getting starting with infrastructure as code in a VMware vSphere environment as easy as possible using HashiCorp Terraform, the de facto standard for infrastructure as code.

Speakers:

- [Kyle Ruddy](https://twitter.com/kmruddy)

Another Infrastructure as Code session I want to see. I really like Hashicorp tools like Terraform and Packer so I'm always interested to see sessions on them. Tools like Terraform are really easy to pick up and applying on even a simple home lab. Kyle is an excellent speaker and so I'm really excited about this session to further my knowledge.

### Place Your Cluster in a Desired State [CODE2752]

Excerpt:

> Discover the new way of updating clusters through configuring and applying a desired state. See what's different and how it brings simplicity and efficiency to your infrastructure.

Speaker:

- Ivaylo Ivanov

Can you spot a pattern in my picks for sessions to attend? I suspect this session is about vSphere Desired State Configuration which I have [blogged about before]({{ site.baseurl }}{% post_url 2019-08-26-vSphereDSC-Part-1 %})). A lot has changed with vSphere DSC since those posts and so I want to get back up to speed on a cool technology that I can practically apply both in my lab and at work.

### Wrap Up

A leaning towards the Code sessions but to be honest they are so interesting. I am sure you will find something in this list that appeals to you, and if not spend some time browsing the [Content Catalog](https://myevents.vmware.com/widget/vmware/vmworld2021/catalog) to find what interests you. With over 900 sessions I guarantee you will.
