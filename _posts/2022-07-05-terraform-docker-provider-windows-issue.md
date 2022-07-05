---
layout: single
classes: wide
title: "Windows Docker Terraform Provider Error"
# date: YYYY-MM-DD
category: [Terraform]
excerpt: "When using Docker for Windows and the Terraform Docker Provider there is a necessary step needed when following the HashiCorp Learn tutorials."
---
## Introduction

The [HashiCorp Learn](https://learn.hashicorp.com/) is a great resource for learning how to use the suite of HashiCorp products. It provides clear, step by step tutorials, videos and interactive sessions on products such as Terraform, Packer, etc. Seriously good stuff.

I was going through the tutorials on the [Terraform CLI](https://learn.hashicorp.com/collections/terraform/cli) and could not get the example running due to this error:

```terraform
C:\repos\learn-terraform-plan>terraform plan -out tfplan
│
│ Error: Error initializing Docker client: protocol not available
│
│   with provider["registry.terraform.io/kreuzwerker/docker"],
│   on main.tf line 1, in provider "docker":
│    1: provider "docker" {}
```

This is a quick post on how to fix this issue, which may be particular to me running Docker Desktop on Windows.

This error occurred when using:

```terraform
C:\repos\learn-terraform-plan>terraform --version
Terraform v1.2.4
on windows_amd64
+ provider registry.terraform.io/hashicorp/random v3.1.0
+ provider registry.terraform.io/kreuzwerker/docker v2.16.0
```

using the examples in the Terraform CLI tutorials. Finally thi was on a Windows 10 install running WSL2 and [Docker Desktop](https://www.docker.com/products/docker-desktop/) v4.10.1.

### The Error

The code example I am using is from the particular tutorial [Create a Terraform Plan](https://learn.hashicorp.com/tutorials/terraform/plan?in=terraform/cli). It uses the following providers and versions:

- kreuzwerker/docker v2.16.0
- hashicorp/random v3.1.0

Looking at the `main.tf` file:

```terraform
provider "docker" {}
provider "random" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "hello-terraform"
  ports {
    internal = 80
    external = 8000
  }
}

resource "random_pet" "dog" {
  length = 2
}

module "nginx-pet" {
  source = "./nginx"

  container_name = "hello-${random_pet.dog.id}"
  nginx_port     = 8001
}

module "hello" {
  source  = "joatmon08/hello/random"
  version = "3.0.1"

  hello      = random_pet.dog.id
  secret_key = var.secret_key
}
```

Pretty straightforward. However when running plan:

```terraform
C:\repos\learn-terraform-plan>terraform plan -out tfplan
│
│ Error: Error initializing Docker client: protocol not available
│
│   with provider["registry.terraform.io/kreuzwerker/docker"],
│   on main.tf line 1, in provider "docker":
│    1: provider "docker" {}
```

### The Fix

Googling the error and a few rabbit holes leads to this [GitHub Issue](https://github.com/hashicorp/terraform-provider-docker/issues/180) from 2019 which describes my issue. The fix is to add to the `provider "docker" {}` section the following:

```terraform
provider "docker" {
 host    = "npipe:////.//pipe//docker_engine"
}
```

Running a plan now:

```terraform
C:\repos\learn-terraform-plan>terraform plan -out tfplan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created

Plan: 7 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Saved the plan to: tfplan

To perform exactly these actions, run the following command to apply:
    terraform apply "tfplan"
```

I have truncated this output, but plan now works.

### The Explanation

I'm no expert in Docker but from research I think what is happening is the provider is looking for the Docker Endpoint on a Linux install. On an Ubuntu machine running [docker context ls](https://docs.docker.com/engine/context/working-with-contexts/):

```bash
cwestwater@docker01:~$ docker context ls
NAME        DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT   ORCHESTRATOR
default *   Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                         swarm
```

compared to the same on my Windows machine:

```dosbatch
C:\repos\learn-terraform-plan>docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT
default *           moby                Current DOCKER_HOST based configuration   npipe:////./pipe/docker_engine
```

Looking at the [Docker Provider documentation](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs) the `host` parameter is:

>host (String) The Docker daemon address

So by adding the Windows Docker Endpoint to the providers block I am forcing it to the Windows Endpoint instead of the Linux one, implying the provider defaults to Linux.

## Wrap Up

I'm probably in the minority of people using Docker on Windows so I'm hoping this post helps anyone else out there in the same situation. An easy fix but for the beginner to Docker and Terraform not an obvious one. If you are using Docker on Windows and Terraform make sure you define the Docker Endpoint.
