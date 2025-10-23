---
title: "Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide"
date: "2024-01-27"
author: "Sujit Mahakhud"
categories: ["Cyber Security"]
tags: ["azure", "azure-sentinel", "cloud-security", "cyber-security", "data-integration", "microsoft", "microsoft-sentinel-implementation", "microsoft-sentinel", "sentinel", "siem"]
description: "A comprehensive step-by-step guide to forwarding Syslog server logs to Microsoft Sentinel. Learn how to set up rsyslog, configure Data Collection Rules (DCR), and install Azure Monitor Agent (AMA) for seamless log integration."
slug: "simplifying-syslog-forwarding-microsoft-sentinel"
original_url: "https://secbyte.in/2024/01/27/simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide/"
seo_title: "Simplify Syslog Forwarding to Microsoft Sentinel | Complete Setup Guide"
seo_description: "Master Syslog forwarding to Microsoft Sentinel with this detailed guide. Configure rsyslog, set up DCR, and integrate Linux logs seamlessly for enhanced security monitoring."
keywords: ["syslog forwarding sentinel", "microsoft sentinel syslog", "azure monitor agent", "data collection rules", "rsyslog configuration", "sentinel log forwarding", "linux syslog sentinel", "AMA agent setup"]
---

# 📡 Simplifying Syslog Forwarding to Microsoft Sentinel: A User-Friendly Guide

Syslog, a common event logging protocol in Linux, can be a powerful asset when seamlessly integrated with Microsoft Sentinel. In this step-by-step guide, we'll break down the process of forwarding Syslog server logs to Microsoft Sentinel, enhancing your cybersecurity capabilities without the complexity.

## 📋 Pre-Requisites

Before we begin, make sure your system meets the following requirements:

- **Hardware:** Your Linux machine needs a minimum of 8 CPU cores and 32 GB RAM
- **Disk Size:** 1 TB
- **Daemon Version:** Rsyslog v8
- **Packages:** Ensure Python 3 is installed
- **Supported Log format:** CEF, Syslog RFC 3164 and Syslog RFC 5424
- **Azure RBAC required:** Contributor role at Resource group level
- **Configuration:** You need elevated permissions (sudo) on your Linux machine, and it shouldn't be connected to any Azure workspaces before installing the AMA agent

**Note:** These infrastructure adjustments are based on the assumption of a forwarder collecting over 250 – 300 GB per day.

## 🏗️ Architecture

When the AMA agent is installed on your VM or appliance, the installation script automatically sets up the local Syslog daemon to send messages to the agent via UDP port 25224. Subsequently, the agent transmits these messages securely over HTTPS to your Log Analytics workspace. Here, they seamlessly integrate into the Syslog table within the Microsoft Sentinel workspace. Log Analytics adeptly collects messages sent by the rsyslog daemons, completing a streamlined process of data ingestion and analysis.

## 🔧 Syslog Forwarder Setup

### 1. Set Syslog Forwarder in Linux

**Install Rsyslog** (if not already installed):

```bash
sudo apt-get update
sudo apt-get install rsyslog
```

**Ensure rsyslog is running:**

```bash
systemctl status rsyslog
```

**Edit rsyslog.conf:**

```bash
sudo nano /etc/rsyslog.conf
```

**Uncomment TCP and UDP for port 514** in the configuration file.

### 2. Create a New Template for Receiving Remote Messages

Let's create a template that will instruct rsyslog server how to store incoming syslog messages. Add the template in the **rsyslog.conf** file just before the **GLOBAL DIRECTIVES** section:

```bash
$template remote-incoming-logs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?remote-incoming-logs
```

**Restart rsyslog:**

```bash
sudo systemctl restart rsyslog
```

## 📊 Adding Data Collection Rules to Microsoft Sentinel

In the quest for seamless log management with Microsoft Sentinel, adding Data Collection Rules (DCRs) becomes a crucial step. Follow these simplified steps to install the Azure Monitor Agent (AMA) and create a Data Collection Rule effortlessly.

### Installing AMA and Creating DCR

#### Using Microsoft Sentinel Portal

1. Open the **Azure portal** and navigate to **Microsoft Sentinel**

2. Go to **Data connectors** from the navigation menu

3. Type **'CEF'** in the Search box and select the **Common Event Format (CEF) via AMA (Preview)** connector
   
   **OR**
   
   For Syslog: type **'Syslog'** in the Search box and select the **Syslog via AMA** connector
   
   (Download from **Content hub** if not available)

4. Open the connector page from the details pane

5. In the Configuration area, click **+Create data collection rule**

6. **In the Basic tab:**
   - Type a DCR name
   - Select your subscription
   - Choose the resource group where your collector is defined

7. Select Next: **Resources >**
   - Define resources **(VMs)**

8. Review your changes and select Next: **Collect >**

9. **Select facilities and severities:**
   - Choose the minimum log level for each facility (Debug logs is recommended)
   - Microsoft Sentinel collects logs for the selected level and higher severity levels

10. Review your selections and select Next: **Review + create**

11. In the Review and create tab, select **Create**

12. The connector will install the **Azure Monitor Agent** on the selected machines
    - Notifications will appear in the Azure portal once the DCR is created and the agent is installed

13. Select **Refresh** on the connector page to view the DCR in the list

## ✅ Verification

After successfully configuring the DCR, you will ingest the logs into the **CommonSecurityLog** and **Syslog** tables, respectively. Additionally, you can check the AMA agent's health status in the **Heartbeat** table.

### Key Points to Verify:

- ✓ AMA agent is installed and running on your Linux VM
- ✓ Data Collection Rule is created and associated with your VM
- ✓ Logs are appearing in the Syslog table in Microsoft Sentinel
- ✓ Heartbeat data confirms agent connectivity

## 🔗 Related Links

- [Microsoft Learn: Connect CEF via AMA](https://learn.microsoft.com/en-us/azure/sentinel/connect-cef-ama?tabs=portal)
- [Microsoft Learn: Data Collection Syslog](https://learn.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-syslog)

## 🎯 Conclusion

Integrating Syslog with Microsoft Sentinel doesn't have to be complex. By following this user-friendly guide, you've successfully set up Syslog forwarding, configured Data Collection Rules, and installed the Azure Monitor Agent. Your Linux logs are now seamlessly flowing into Microsoft Sentinel, enhancing your security monitoring capabilities.

---

## 📞 Contact Information

**Follow us on LinkedIn:** [www.linkedin.com/in/sujitmahakhud](http://www.linkedin.com/in/sujitmahakhud)

**Email:** www.sujitmahakhud@gmail.com

**YouTube:** [https://www.youtube.com/@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)

---

📚 **Read this article on the official website:** [SecByte.in](https://secbyte.in/2024/01/27/simplifying-syslog-forwarding-to-microsoft-sentinel-a-user-friendly-guide/)
