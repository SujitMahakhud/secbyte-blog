---
title: "Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues"
date: "2024-06-06"
author: "Sujit Mahakhud"
categories: ['Automation', 'azure', 'Azure Sentinel', 'Cyber Security', 'incident management', 'Microsoft', 'Microsoft security', 'microsoft-sentinel']
tags: ['Automation', 'azure', 'Azure security', 'Azure Sentinel', 'Cloud Security', 'Cyber Security', 'Data integration', 'Linux', 'Log Ingestion', 'Microsoft', 'Microsoft security', 'microsoft-sentinel', 'siem', 'troubleshoot', 'troubleshooting']
description: "Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues - Comprehensive guide for cybersecurity professionals."
slug: "troubleshoot-log-ingestion-drops-to-microsoft-sentinel-from-linux-machines-addressing-varlog-capacity-issues"
original_url: "https://secbyte.in/2024/06/06/troubleshoot-log-ingestion-drops-to-microsoft-sentinel-from-linux-machines-addressing-var-log-capacity-issues/"
seo_title: "Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues | SecByte"
seo_description: "Learn about Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues with this detailed guide from SecByte."
keywords: ['automation', 'azure', 'azure security', 'azure sentinel', 'cloud security']
---

# Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues


``````


### Why Does This Happen?


``````


### Solution: Automate Log Deletion with a Cron Job


``````


### Step 1: Modify rsyslog.conf


``````


``````


``````

- Add the following template below the TCP and UDP recipients:


``````


### Step 2: Create a Log Maintenance Script

Next, create a script that will delete the logs.


``````


``````


``````


``````

- Add the following command to the script, which will truncate the log files:


``````


``````


### Step 3: Schedule the Cron Job

Finally, create a cron job to run the script at regular intervals.

- Open the crontab editor:


``````

- Add the following lines to schedule the script to run four times a day:


``````


## Explanation of Commands


``````


### Conclusion


``````


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

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2024/06/06/troubleshoot-log-ingestion-drops-to-microsoft-sentinel-from-linux-machines-addressing-var-log-capacity-issues/)

---

*Last Updated: 2024-06-06*  
*Author: Sujit Mahakhud | Cybersecurity Specialist*
