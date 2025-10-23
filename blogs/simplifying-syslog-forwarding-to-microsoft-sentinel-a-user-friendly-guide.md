---
title: "Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide"
date: "2024-01-27"
author: "Sujit Mahakhud"
description: "Syslog, a common event logging protocol in Linux, can be a powerful asset when seamlessly integrated with Microsoft Sentinel. In this step-by-step gui..."
slug: "simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide"
original_url: "https://secbyte.in/2024/01/27/simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide/"
---

# Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide

Syslog, a common event logging protocol in Linux, can be a powerful asset when seamlessly integrated with Microsoft Sentinel. In this step-by-step guide, we’ll break down the process of forwarding Syslog server logs to Microsoft Sentinel, enhancing your cybersecurity capabilities without the complexity.


## Pre-Requisites:
Before we begin, make sure your system meets the following requirements:

- Hardware: Your Linux machine needs a minimum of 8 CPU cores and 32 GB RAM.
- Disk Size: 1 TB
- Daemon Version: Rsyslog v8.
- Packages: Ensure Python 3 is installed.
- Supported Log format: CEF, Syslog RFC 3164 and Syslog RFC 5424.
- Azure RBAC required: Contributor role at Resouce group level
- Configuration: You need elevated permissions (sudo) on your Linux machine, and it shouldn’t be connected to any Azure workspaces before installing the AMA agent.
Note: These infrastructure adjustments are based on the assumption of a forwarder collecting over 250 – 300 GB per day.


## Architecture:

![Image](https://secbyte.in/wp-content/uploads/2024/01/syslog-forwarder-diagram.png?w=1024)
When the AMA agent is installed on your VM or appliance, the installation script automatically sets up the local Syslog daemon to send messages to the agent via UDP port 25224. Subsequently, the agent transmits these messages securely over HTTPS to your Log Analytics workspace. Here, they seamlessly integrate into the Syslog table within the Microsoft Sentinel workspace. Log Analytics adeptly collects messages sent by the rsyslog daemons, completing a streamlined process of data ingestion and analysis.


## Syslog Forwarder Setup:

### 1. Set Syslog Forwarder in Linux:
- Rsyslog is typically installed. If not, install it using: sudo apt-get update sudo apt-get install rsyslog
- sudo apt-get update
- sudo apt-get install rsyslog
- Ensure rsyslog is running: systemctl status rsyslog
- systemctl status rsyslog
- Edit rsyslog.conf: sudo nano /etc/rsyslog.conf
- sudo nano /etc/rsyslog.conf
- Uncomment TCP and UDP for port 514.
- sudo apt-get update
- sudo apt-get install rsyslog
- systemctl status rsyslog
- sudo nano /etc/rsyslog.conf

### 2. Create a New Template for Receiving Remote Messages:
- Let’s create a template that will instruct rsyslog server how to store incoming syslog messages. Add the template in the rsyslog.conf file just before GLOBAL DIRECTIVES section: $template remote-incoming-logs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log" *.* ?remote-incoming-logs
- $template remote-incoming-logs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log" *.* ?remote-incoming-logs
- Restart rsyslog: sudo systemctl restart rsyslog.
- $template remote-incoming-logs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log" *.* ?remote-incoming-logs

## Adding Data Collection Rules to Microsoft Sentinel
In the quest for seamless log management with Microsoft Sentinel, adding Data Collection Rules (DCRs) becomes a crucial step. Follow these simplified steps to install the Azure Monitor Agent (AMA) and create a Data Collection Rule effortlessly.


### Installing AMA and Creating DCR
- Type a DCR name.
- Select your subscription.
- Choose the resource group where your collector is defined.
- Define resources (VMs)
- Choose the minimum log level for each facility. (Debug logs is recommended)
- Microsoft Sentinel collects logs for the selected level and higher severity levels.
- Notifications will appear in the Azure portal once the DCR is created and the agent is installed.
After successfully configuring the DCR, you will ingest the logs into the CommonSecurityLog and Syslog tables, respectively. Additionally, you can check the AMA agent’s health status in the Heartbeat table.

- https://learn.microsoft.com/en-us/azure/sentinel/connect-cef-ama?tabs=portal
- https://learn.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-syslog
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
