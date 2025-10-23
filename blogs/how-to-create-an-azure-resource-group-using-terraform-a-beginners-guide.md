---
title: "How to Create an Azure Resource Group Using Terraform — A Beginner’s Guide"
date: "2025-06-04"
author: "Sujit Mahakhud"
description: "If you’re new to Terraform and Azure, getting started can feel a little overwhelming. But don’t worry — creating your first Azure Resource Group with ..."
slug: "how-to-create-an-azure-resource-group-using-terraform-a-beginners-guide"
original_url: "https://secbyte.in/2025/06/04/how-to-create-an-azure-resource-group-using-terraform-a-beginners-guide/"
---

# How to Create an Azure Resource Group Using Terraform — A Beginner’s Guide

If you’re new to Terraform and Azure, getting started can feel a little overwhelming. But don’t worry — creating your first Azure Resource Group with Terraform is simpler than you think! In this guide, I’ll walk you through the exact steps to set up your environment and deploy a resource group on Azure using Terraform.

Let’s jump right in! 🚀


## What Is Terraform & Why Use It?
Terraform is an open-source Infrastructure as Code (IaC) tool. It lets you write code to create, manage, and update cloud resources — like virtual machines, storage accounts, and resource groups — instead of clicking through a web portal.

Using Terraform means your infrastructure is version-controlled, repeatable, and easy to share or modify. It’s a game-changer for beginners and pros alike.


## Prerequisites Before We Begin
To follow along, make sure you have:

- An Azure account (a free tier works just fine)
- Terraform installed on your machine (download from terraform.io)
- Azure CLI installed (optional but helpful for authentication)
- A Service Principal in Azure with appropriate permissions — this acts like a user Terraform uses to authenticate with Azure securely

## Step 1: Set Up Your Azure Service Principal
You can create a Service Principal — which acts like a “robot user” for Terraform — using the Azure Portal:

That’s it! Now you have the details needed to authenticate Terraform with Azure.


## Step 2: Write Your Terraform Configuration
Create a new folder on your computer for your Terraform project. Inside it, create a file named main.tf and add this code:


```hcl
provider "azurerm" {
  features {}

  subscription_id = "<your-subscription-id>"
  client_id       = "<your-appId>"
  client_secret   = "<your-password>"
  tenant_id       = "<your-tenant>"
}

resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"
  location = "West Europe"
}
```
Make sure you replace the placeholders (<your-subscription-id>, etc.) with the values from your service principal.


## Step 3: Initialize Terraform
Open your terminal (or PowerShell) and navigate to your Terraform project folder. Run:


```
terraform init
```
This downloads the Azure provider and prepares Terraform to work with your configuration.


## Step 4: Apply Your Configuration
Now you’re ready to create the resource group! Run:


```
terraform apply
```
Terraform will show you a preview of what it plans to create. Review it, and if it looks good, type yes and press Enter.


## Step 5: Verify Your Resource Group in Azure
Once Terraform finishes, head over to the Azure Portal and check under Resource Groups. You should see your new resource group created exactly where you specified it — “West Europe” in this example!


## Bonus: Using Environment Variables for Security
Hardcoding sensitive information like your client secret in code can be risky. Instead, you can set environment variables on your machine like this:

On PowerShell:


```
$env:ARM_SUBSCRIPTION_ID = "<your-subscription-id>"
$env:ARM_CLIENT_ID       = "<your-appId>"
$env:ARM_CLIENT_SECRET   = "<your-password>"
$env:ARM_TENANT_ID       = "<your-tenant>"
```
Then update your main.tf to:


```hcl
provider "azurerm" {
  features {}
}

```
Terraform will automatically pick up the values from your environment variables. This keeps secrets out of your code — a best practice for every Terraform project!


## Wrapping Up
And there you have it — your very first Azure Resource Group created using Terraform! 🎉

Terraform lets you manage your cloud infrastructure with code, which means no more manual clicks, fewer mistakes, and a path to automation and scalability.

Ready to take it further? Next up, try creating virtual machines, storage accounts, or even entire networks with Terraform!


### If you enjoyed this beginner-friendly guide, share it with a friend or drop a comment below. I love hearing your feedback and questions!
Go back


![Image](https://secbyte.in)

![Image](https://secbyte.indata:image/gif;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=)
- 
Δ


### Share this:
- Click to share on X (Opens in new window) X
- Click to share on Facebook (Opens in new window) Facebook
- Click to share on LinkedIn (Opens in new window) LinkedIn
- Click to share on Telegram (Opens in new window) Telegram
- Click to share on Threads (Opens in new window) Threads
- Click to share on WhatsApp (Opens in new window) WhatsApp
- Click to share on X (Opens in new window) X
- Click to share on Mastodon (Opens in new window) Mastodon
- 


---
💬 **Connect with Me**
- [LinkedIn](https://www.linkedin.com/in/sujitmahakhud/)
- [YouTube](https://www.youtube.com/@sujitmahakhud_official)
