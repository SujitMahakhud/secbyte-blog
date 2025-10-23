---
title: "Automate Azure Infrastructure with Terraform: A Beginner's Guide"
date: "2025-07-02"
author: "Sujit Mahakhud"
categories: ["Azure", "Terraform", "DevOps", "Cloud", "Infrastructure as Code"]
tags: ["azure", "terraform", "devops", "cloud infrastructure", "IaC", "log analytics", "resource group"]
description: "Step-by-step beginner-friendly guide to automating Azure infrastructure using Terraform. Learn how to create Resource Groups and Log Analytics Workspaces with reusable variables."
slug: "automate-azure-infrastructure-with-terraform-beginners-guide"
featured_image: ""
original_url: "https://secbyte.in/2025/07/02/automate-azure-infrastructure-with-terraform-a-beginners-guide/"
seo_title: "Automate Azure Infrastructure with Terraform - Beginner's Guide 2025"
seo_description: "Learn how to automate Azure infrastructure with Terraform. Complete beginner tutorial covering Resource Groups, Log Analytics Workspaces, and Terraform variables."
keywords: ["terraform azure tutorial", "azure infrastructure automation", "terraform beginner guide", "azure resource group terraform", "log analytics workspace terraform", "infrastructure as code azure"]
---

# Automate Azure Infrastructure with Terraform: A Beginner's Guide

## 🌐 Introduction

If you're new to Terraform and want to learn how to automate your Azure infrastructure, you're in the right place! In this blog, we'll walk you through how to create:

- A **Resource Group**
- A **Log Analytics Workspace**
- Both using **Terraform variables** for flexibility and reusability

And the best part? You don't need to be an expert — this guide is beginner-friendly and hands-on.

---

## 🔧 What is Terraform?

**Terraform** is an **open-source Infrastructure as Code (IaC)** tool developed by HashiCorp. It lets you write code to define and manage cloud infrastructure in a **safe, consistent, and repeatable** way.

### Why Use Terraform?

- **Declarative Syntax**: Define what you want, not how to create it
- **Cloud-Agnostic**: Works with Azure, AWS, GCP, and more
- **Version Control**: Track infrastructure changes using Git
- **Automation Ready**: Integrate with CI/CD pipelines
- **Repeatable Deployments**: Deploy the same infrastructure across multiple environments

---

## 🎯 What Will We Build?

In this tutorial, we'll create:

1. A **Resource Group** in Azure (a logical container for your resources)
2. A **Log Analytics Workspace** (the foundation for Microsoft Sentinel and Azure Monitor)
3. Reusable configuration using **Terraform variables**

---

## 📁 Step-by-Step Project Setup

Let's get started!

### ✅ Step 1: Create a Working Folder

First, create a new folder on your computer for your Terraform project:

```bash
mkdir sentinel-deploy
cd sentinel-deploy
```

### ✅ Step 2: Create Required Files

Inside your folder, create these 3 files:

| File Name | Purpose |
|-----------|---------|
| `main.tf` | The main Terraform configuration (what to build) |
| `variables.tf` | Holds input values like resource names, locations |
| `outputs.tf` | (Optional) Displays output values after deployment |

You can create these using a text editor like VS Code, Notepad++, or even Notepad.

---

## 📄 File 1: `main.tf`

This is where you define **what Terraform will create**.

```hcl
provider "azurerm" {
  features {}
}

# Create a new Resource Group
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

# Create a Log Analytics Workspace
resource "azurerm_log_analytics_workspace" "law" {
  name                = var.workspace_name
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}
```

### ✅ What This Does:

- **Connects to Azure** using the AzureRM provider
- **Creates a Resource Group** with a name and location from variables
- **Creates a Log Analytics Workspace** inside that Resource Group with 30-day log retention

---

## 📄 File 2: `variables.tf`

This is where you define **user-configurable variables** and give them default values.

```hcl
variable "location" {
  description = "Azure region for resources"
  type        = string
  default     = "South Central US"
}

variable "resource_group_name" {
  description = "Name of the Resource Group"
  type        = string
  default     = "sentinel-demo-rg"
}

variable "workspace_name" {
  description = "Name of the Log Analytics Workspace"
  type        = string
  default     = "sentinel-law-demo"
}
```

### ✅ Benefits of Using Variables:

- **Reusability**: Change values without modifying main configuration
- **Environment-Specific**: Use different values for dev, test, and production
- **Flexibility**: Override defaults when running Terraform commands

---

## 📄 File 3: `outputs.tf` (Optional)

This file is optional, but it helps you **see useful information** after your deployment.

```hcl
output "resource_group_name" {
  value       = azurerm_resource_group.rg.name
  description = "The name of the Resource Group"
}

output "workspace_id" {
  value       = azurerm_log_analytics_workspace.law.id
  description = "The ID of the Log Analytics Workspace"
}

output "workspace_name" {
  value       = azurerm_log_analytics_workspace.law.name
  description = "The name of the Log Analytics Workspace"
}
```

### ✅ Why Use Outputs?

Outputs display important information after deployment, such as resource IDs that you might need for other configurations or integrations.

---

## 🛠️ Step 3: Initialize Terraform

Open your terminal or PowerShell in the project directory and run:

```bash
terraform init
```

### 📌 What This Does:

- Downloads the Azure provider plugin
- Initializes the working directory
- Prepares Terraform for deployment

You should see output indicating successful initialization.

---

## 🔍 Step 4: Review the Plan

Before applying changes, always review what Terraform plans to do:

```bash
terraform plan
```

### 📌 What This Shows:

- Resources that will be **created** (marked with `+`)
- Resources that will be **modified** (marked with `~`)
- Resources that will be **destroyed** (marked with `-`)
- A summary of changes

This is your **dry-run preview** — no changes are made at this stage.

---

## 🚀 Step 5: Apply the Plan

Now let's deploy the infrastructure!

```bash
terraform apply
```

Terraform will:
1. Show you the execution plan again
2. Ask for confirmation
3. Type `yes` and hit Enter

### ✅ Expected Results:

In a minute or two, you'll have:
- A new **Resource Group** in Azure
- A **Log Analytics Workspace** configured and ready

You can verify this in the Azure Portal by navigating to Resource Groups.

---

## 🧹 Step 6 (Optional): Destroy the Resources

If you want to remove everything Terraform created (useful for testing), use:

```bash
terraform destroy
```

⚠️ **Warning**: This will permanently delete all resources managed by this Terraform configuration. Type `yes` to confirm.

---

## 📊 Summary Table

| What We Did | Tool/Command Used |
|-------------|-------------------|
| Defined Azure resources as code | `main.tf` |
| Made the code flexible with variables | `variables.tf` |
| Displayed deployment outputs | `outputs.tf` |
| Initialized Terraform | `terraform init` |
| Previewed changes | `terraform plan` |
| Deployed infrastructure | `terraform apply` |
| Cleaned up resources | `terraform destroy` |

---

## 🧠 Why This is Powerful

You just automated part of your Azure infrastructure! Here's why this matters:

### ✅ Key Benefits:

1. **Repeatable**: Run the same code in multiple environments
2. **Auditable**: Track every change through version control
3. **Consistent**: Eliminate manual configuration errors
4. **Collaborative**: Share infrastructure code with your team
5. **Scalable**: Easily expand to manage hundreds of resources

### 💡 Real-World Use Cases:

- **Development Environments**: Quickly spin up and tear down test environments
- **Disaster Recovery**: Rebuild infrastructure in minutes
- **Multi-Region Deployments**: Deploy identical infrastructure across regions
- **Microsoft Sentinel Setup**: Foundation for security monitoring infrastructure

---

## 🙌 Next Steps

Now that you've mastered the basics, here's what to explore next:

### Upcoming Topics:

1. **Enabling Microsoft Sentinel** on your Log Analytics Workspace
2. **Adding Data Connectors** to ingest security logs
3. **Creating Analytics Rules** for threat detection
4. **Automating Incident Response** with Logic Apps
5. **Managing Terraform State** with remote backends
6. **Using Terraform Modules** for reusable components

### 📚 Additional Learning Resources:

- [Terraform Azure Provider Documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure Log Analytics Overview](https://learn.microsoft.com/azure/azure-monitor/logs/log-analytics-overview)
- [Microsoft Sentinel Documentation](https://learn.microsoft.com/azure/sentinel/)

---

## 💬 Got Questions?

I'm here to help! Feel free to:

- **Leave a comment** below with your questions
- **Connect on LinkedIn**: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- **Subscribe on YouTube**: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- **Email me**: sujitmahakhud@gmail.com

I also offer **one-on-one training** on:
- Microsoft Sentinel
- Azure Logic Apps
- KQL (Kusto Query Language)
- Azure Security & Compliance

---

## 🎓 Key Takeaways

✅ Terraform enables Infrastructure as Code for Azure  
✅ Variables make configurations reusable and flexible  
✅ Always review with `terraform plan` before applying  
✅ Log Analytics Workspace is the foundation for Sentinel  
✅ Version control your Terraform code with Git  

---

## 📖 Related Posts

- [How to Configure Azure Blob Storage as Terraform Remote Backend](https://secbyte.in/)
- [KQL Queries for Microsoft Sentinel](https://secbyte.in/)
- [Automating Security Operations in Azure](https://secbyte.in/)

---

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2025/07/02/automate-azure-infrastructure-with-terraform-a-beginners-guide/)

---

*Last Updated: July 2, 2025*  
*Author: Sujit Mahakhud | Cybersecurity Consultant & Microsoft Sentinel Specialist*
