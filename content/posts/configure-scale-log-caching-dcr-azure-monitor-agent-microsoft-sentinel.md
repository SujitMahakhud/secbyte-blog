---
title: "How to Configure or Scale Log Caching DCR in Azure Monitor Agent for Microsoft Sentinel"
date: "2025-06-30"
author: "Sujit Mahakhud"
tags: ["AMA", "Azure", "Cloud", "DCR", "Microsoft", "Microsoft Sentinel", "SQL Server", "Technology"]
categories: ["Azure Sentinel", "Microsoft Sentinel"]
original_url: "https://secbyte.in/2025/06/30/1782/"
---

# How to Configure or Scale Log Caching DCR in Azure Monitor Agent for Microsoft Sentinel

## 📘 Introduction

If you're using **Azure Monitor Agent (AMA)** for collecting logs from virtual machines, it's important to understand the concept of **log caching using Data Collection Rules (DCRs)**.

In this blog, you'll learn:

- What Log Caching in AMA is
- When and why it's needed
- Default vs. extended caching limits (up to 1 TB)
- AMA versions that support this feature
- How to plan and size it for your environment
- Step-by-step setup with screenshots and code

## 💡 What is Log Caching in Azure Monitor Agent?

Azure Monitor Agent collects logs and metrics from your Azure VMs and forwards them to **Log Analytics**. But what happens when there's **no network** or **temporary disruption**?

This is where **log caching** comes in.

When you configure an **AgentSettings DCR**, AMA will **cache logs locally** on the machine if it's unable to send them immediately. It will then **forward the cached data** once connectivity is restored.

## 🛠️ Why and When is Log Caching Required?

Log caching is critical in scenarios such as:

- ⚡ **Network or Azure region outages**
- 🧩 **On-premises or hybrid VMs with unreliable internet**
- 📈 **High ingestion bursts that cause backlog**

By default, AMA buffers up to **10 GB** of logs for ~72 hours. But in some environments, that's just not enough.

## 📏 Default Cache Limit vs. 1 TB Expansion

| Type | Limit | Supported AMA Version |
|---|---|---|
| **Default** | 10,000 MB (10 GB) | All |
| **Extended** | Up to 1,000,000 MB (1 TB) | Windows ≥ 1.34.0   Linux ≥ 1.34.5 |

✅ **Note**: This feature is currently in **preview** and requires configuring a special **AgentSettings DCR** via **ARM templates** or **CLI**. Portal support is limited.

## 🧮 How to Plan Log Caching for Your Environment

Before enabling large cache limits, calculate:

```
Cache Needed (MB) = Event Rate × Event Size × Duration
```

Example:

- Event rate: 5,000 events/sec
- Avg. size: 200 bytes
- Outage duration: 24 hours

> **Total** ≈ 5,000 × 200 × 86400 = 86.4 GB

💡 Tip: Leave a margin (10–15%) for overhead and unexpected spikes.

Also ensure:

- Enough **free disk space** on the AMA cache path:
  - **Windows**: `C:\ProgramData\Azure Monitor Agent\`
  - **Linux**: `/var/opt/microsoft/azuremonitoragent/`

## 🧰 How to Create a Log Caching DCR (Step-by-Step)

This example sets the cache to **500 GB**.

### 1️⃣ Prepare JSON Template

```json
{
  "type": "Microsoft.Insights/dataCollectionRules",
  "name": "dcr-logcache-example",
  "apiVersion": "2023-03-11",
  "kind": "AgentSettings",
  "location": "eastus",
  "properties": {
    "description": "Increase offline log cache to 500 GB",
    "agentSettings": {
      "logs": [
        { "name": "MaxDiskQuotaInMB", "value": "500000" }
      ]
    }
  }
}
```

### 2️⃣ Deploy DCR via Azure CLI

```bash
az monitor data-collection rule create \
  --resource-group rg-name \
  --name dcr-logcache-example \
  --location eastus \
  --kind AgentSettings \
  --body @log-cache-dcr.json
```

### 3️⃣ Associate DCR to VM

```bash
az monitor data-collection rule association create \
  --name agentSettingsAssoc \
  --rule-id /subscriptions/xxxx/resourceGroups/rg-name/providers/Microsoft.Insights/dataCollectionRules/dcr-logcache-example \
  --resource /subscriptions/xxxx/resourceGroups/rg-name/providers/Microsoft.Compute/virtualMachines/vm-name
```

### 4️⃣ Restart AMA Agent (Optional)

- **Windows**: `net stop AzureMonitorAgent && net start AzureMonitorAgent`
- **Linux**: `sudo systemctl restart azuremonitoragent`

## 🛠️ Troubleshooting Tips

| Issue | Resolution |
|---|---|
| Log cache not working | Check AMA version (must be ≥ 1.34.x) |
| Disk full | Expand partition that stores the AMA cache |
| Cache not applied | Ensure only **one AgentSettings DCR** per VM |
| No data uploaded after reconnection | Logs upload from **(now – 1 hour)**, expect slight delay |

## 🧾 Summary

🔹 Log caching via **AgentSettings DCR** makes AMA more resilient.  
🔹 Helps prevent data loss during outages or disconnections.  
🔹 Default limit is **10 GB**, extendable up to **1 TB**.  
🔹 Requires **Azure Monitor Agent v1.34+**.  
🔹 Must be created via ARM or CLI (portal support is limited for AgentSettings).

## 📣 Call to Action

💬 Have questions or want to automate your Azure Monitor deployments?  
📧 Reach out or drop a comment below!

🔗 Bookmark [secbyte.in](https://secbyte.in/) for more Microsoft Sentinel and Azure security blogs.

---

**Want to learn more about Microsoft Sentinel?**

- Connect on LinkedIn: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- Subscribe on YouTube: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- Email me: [sujitmahakhud@gmail.com](mailto:sujitmahakhud@gmail.com)

🧩 Read this article on the official website: [secbyte.in](https://secbyte.in/2025/06/30/1782/)
