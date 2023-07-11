# terraform_IaaC

Created: July 11, 2023 9:28 AM
Tags: Work
URL: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli

<aside>
💡 **Notion Tip:** Create a new page and select `Daily entry` ****from the list of template options to automatically generate the format below every day.

</aside>

# Introduction

Infrastructure as code (IaaC) tools allow you to manage infrastructure with configuration files rather than through a graphical user interface. IaC allows you to build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share.

Terraform is HashiCorp's infrastructure as code tool. It lets you define resources and infrastructure in human-readable, declarative configuration files, and manages your infrastructure's lifecycle. Using Terraform has several advantages over manually managing your infrastructure:

- Terraform can manage infrastructure on multiple cloud platforms.
- The human-readable configuration language helps you write infrastructure code quickly.
- Terraform's state allows you to track resource changes throughout your deployments.
- You can commit your configurations to version control to safely collaborate on infrastructure.

## Deployment Workflow

Terraform's configuration language is declarative, meaning that it describes the desired end-state for your infrastructure, in contrast to procedural programming languages that require step-by-step instructions to perform tasks. Terraform providers automatically calculate dependencies between resources to create or destroy them in the correct order.

![Untitled](terraform_IaaC%20fb53fb5a0c43421e925a6593253a1e10/Untitled.png)

To deploy infrastructure with Terraform:

- **Scope** - Identify the infrastructure for your project.
- **Author** - Write the configuration for your infrastructure.
- **Initialize** - Install the plugins Terraform needs to manage the infrastructure.
- **Plan** - Preview the changes Terraform will make to match your configuration.
- **Apply** - Make the planned changes.

# Installation

## 1. chocolatey on Windows

```powershell
choco install terraform
```

To verify installation:

```powershell
terraform -help

terraform -help plan
```

## 2. Linux - Ubuntu/Debian

Ensure that your system is up to date and you have installed the `gnupg`, `software-properties-common`, and `curl` packages installed. You will use these packages to verify HashiCorp's GPG signature and install HashiCorp's Debian package repository.

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
```

Install the HashiCorp GPG Key

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

verify the key’s fingerprint

```bash
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
```

Add the official HashiCorp repository to your system. The `lsb_release -cs` command finds the distribution release codename for your current system, such as `buster`, `groovy`, or `sid`.

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Download the package information from HashiCorp.

```bash
sudo apt update
```

install terraform from the new repository

```bash
sudo apt-get install terraform
```

to verify the installation just like on windows we do, 

```bash
terraform -help
```

# Getting started

# simple docker container with NGINX

1. create a directory that will house the configuration  files that you write to describe the infrastructure you want terraform to create an manage.
    
    When you initialize and apply the configuration here, Terraform uses this directory to store the required plugins, modules(pre-written configurations), and information about the real infrastructure it created 
    
    ```bash
    mkdir learn-terraform-docker-container
    
    cd learn-terraform-docker-container
    ```
    
    in the directory above create a file called `main.tf` and paste the following configuration text 
    
    - remember to use a linux terminal (Ubuntu) if you are using windows, a WSL terminal will work just fine
    
    ```json
    terraform {
      required_providers {
        docker = {
          source  = "kreuzwerker/docker"
          version = "~> 3.0.1"
        }
      }
    }
    
    provider "docker" {}
    
    resource "docker_image" "nginx" {
      name         = "nginx"
      keep_locally = false
    }
    
    resource "docker_container" "nginx" {
      image = docker_image.nginx.image_id
      name  = "tutorial"
    
      ports {
        internal = 80
        external = 8000
      }
    }
    ```
    

initialize the project which downloads a plugin called provider that lets Terraform interact with Docker

- For Docker installation check journal notes on it above

```bash
terraform init
```

Provision the NGINX server container with `apply`. When Terraform asks you to confirm type `yes` and press `ENTER`.

```bash
terraform apply
```

Verify the existence of the NGINX container by visiting [localhost:8000](http://localhost:8000/) in your web browser or running `docker ps` to see the container.

![Untitled](terraform_IaaC%20fb53fb5a0c43421e925a6593253a1e10/Untitled%201.png)

to stop the container, run the below command;

```bash
terraform destroy
```

