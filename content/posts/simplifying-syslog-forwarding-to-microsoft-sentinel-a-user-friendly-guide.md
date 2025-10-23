---
title: "Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide"
date: "2024-01-27"
author: "Sujit Mahakhud"
categories: ['Cyber Security']
tags: ['azure', 'Azure Sentinel', 'Cloud Security', 'Cyber Security', 'Data integration', 'Microsoft', 'Microsoft sentinel Implementation', 'microsoft-sentinel', 'sentinel', 'siem']
description: "Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide - Comprehensive guide for cybersecurity professionals."
slug: "simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide"
original_url: "https://secbyte.in/2024/01/27/simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide/"
seo_title: "Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide | SecByte"
seo_description: "Learn about Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide with this detailed guide from SecByte."
keywords: ['azure', 'azure sentinel', 'cloud security', 'cyber security', 'data integration']
---

# Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide

Syslog, a common event logging protocol in Linux, can be a powerful asset when seamlessly integrated with Microsoft Sentinel. In this step-by-step guide, we’ll break down the process of forwarding Syslog server logs to Microsoft Sentinel, enhancing your cybersecurity capabilities without the complexity.


## Pre-Requisites:

Before we begin, make sure your system meets the following requirements:

- Hardware:Your Linux machine needs a minimum of 8 CPU cores and 32 GB RAM.
- Disk Size:1 TB
- Daemon Version:Rsyslog v8.
- Packages:Ensure Python 3 is installed.
- Supported Log format:CEF, Syslog RFC 3164 and Syslog RFC 5424.
- Azure RBAC required:Contributor role at Resouce group level
- Configuration:You need elevated permissions (sudo) on your Linux machine, and it shouldn’t be connected to any Azure workspaces before installing the AMA agent.

Note: These infrastructure adjustments are based on the assumption of a forwarder collecting over 250 – 300 GB per day.


## Architecture:

When the AMA agent is installed on your VM or appliance, the installation script automatically sets up the local Syslog daemon to send messages to the agent via UDP port 25224. Subsequently, the agent transmits these messages securely over HTTPS to your Log Analytics workspace. Here, they seamlessly integrate into the Syslog table within the Microsoft Sentinel workspace. Log Analytics adeptly collects messages sent by the rsyslog daemons, completing a streamlined process of data ingestion and analysis.


## Syslog Forwarder Setup:


### 1. Set Syslog Forwarder in Linux:


``````


``````


``````


``````


### 2. Create a New Template for Receiving Remote Messages:


``````


``````


## Adding Data Collection Rules to Microsoft Sentinel

In the quest for seamless log management with Microsoft Sentinel, adding Data Collection Rules (DCRs) becomes a crucial step. Follow these simplified steps to install the Azure Monitor Agent (AMA) and create a Data Collection Rule effortlessly.


### Installing AMA and Creating DCR


#### Using Microsoft Sentinel Portal

- Open theAzure portaland navigate toMicrosoft Sentinel.
- Go toData connectorsfrom the navigation menu.
- Type‘CEF’in the Search box and select theCommon Event Format (CEF) via AMA (Preview)connector.ORfor Syslog: type‘Syslog’in the Search box and select the Syslog via AMA connector. (Download fromContent hubif not available)
- Open the connector page from the details pane.
- In the Configuration area, click+Create data collection rule.
- In the Basic tab:Type a DCR name.Select your subscription.Choose the resource group where your collector is defined.
- Select Next:Resources >.Define resources(VMs)
- Review your changes and select Next:Collect >.
- Select facilities and severities:Choose the minimum log level for each facility. (Debug logs is recommended)Microsoft Sentinel collects logs for the selected level and higher severity levels.
- Review your selections and select Next:Review + create.
- In the Review and create tab, selectCreate.
- The connector will install theAzure Monitor Agenton the selected machines.Notifications will appear in the Azure portal once the DCR is created and the agent is installed.
- Select Refresh on the connector page to view the DCR in the list.

- Type a DCR name.
- Select your subscription.
- Choose the resource group where your collector is defined.

- Define resources(VMs)

- Choose the minimum log level for each facility. (Debug logs is recommended)
- Microsoft Sentinel collects logs for the selected level and higher severity levels.

- Notifications will appear in the Azure portal once the DCR is created and the agent is installed.

After successfully configuring the DCR, you will ingest the logs into theCommonSecurityLogandSyslogtables, respectively. Additionally, you can check the AMA agent’s health status in theHeartbeattable.


#### Related Links:

- https://learn.microsoft.com/en-us/azure/sentinel/connect-cef-ama?tabs=portal

- https://learn.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-syslog

Go back


#### Your message has been sent


Δ


### Share this:

- Click to share on X (Opens in new window)X
- Click to share on Facebook (Opens in new window)Facebook
- Click to share on LinkedIn (Opens in new window)LinkedIn
- Click to share on Telegram (Opens in new window)Telegram
- Click to share on Threads (Opens in new window)Threads
- Click to share on WhatsApp (Opens in new window)WhatsApp
- Click to share on X (Opens in new window)X
- Click to share on Mastodon (Opens in new window)Mastodon
- 



---

## 💬 Got Questions?

- **Connect on LinkedIn**: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- **Subscribe on YouTube**: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- **Email**: sujitmahakhud@gmail.com

---

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2024/01/27/simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide/)

---

*Last Updated: 2024-01-27*  
*Author: Sujit Mahakhud | Cybersecurity Specialist*
