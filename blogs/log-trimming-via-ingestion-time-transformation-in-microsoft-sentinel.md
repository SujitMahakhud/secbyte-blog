---
title: "Log Trimming via Ingestion time transformation in Microsoft Sentinel"
date: "2024-02-12"
author: "Sujit Mahakhud"
description: "Microsoft Sentinel, powered by Azure Monitor’s Log Analytics, serves as a pivotal platform for security monitoring and threat detection. All incoming ..."
slug: "log-trimming-via-ingestion-time-transformation-in-microsoft-sentinel"
original_url: "https://secbyte.in/2024/02/12/log-trimming-via-ingestion-time-transformation-in-microsoft-sentinel/"
---

# Log Trimming via Ingestion time transformation in Microsoft Sentinel

Microsoft Sentinel, powered by Azure Monitor’s Log Analytics, serves as a pivotal platform for security monitoring and threat detection. All incoming logs are channeled through Microsoft Sentinel and stored in Log Analytics Workspace, forming a centralized repository for efficient log management and analysis using Kusto Query Language (KQL).

Log Analytics provides users with customizable data ingestion capabilities through Data Collection Rules (DCRs), allowing for tailored data processing and management. Among these capabilities is log trimming, a process that removes extraneous information from log data, ensuring streamlined storage and facilitating precise analysis. Furthermore, Log Analytics supports filtering, enrichment, and customization of data tables, accommodating diverse log formats from various sources.


## Benefits of Log Trimming
Log trimming in Microsoft Sentinel offers several advantages for organizations:

- Cost Optimization: By eliminating unnecessary data, log trimming reduces storage and data ingestion costs, optimizing resource utilization and driving cost savings.
- Enhanced Log Management: Focusing on essential data improves query performance, reduces noise, and simplifies compliance management, facilitating smoother log retention and archiving processes.
- Improved Security Analysis: Cleaner, more relevant data sets enable security analysts to swiftly detect and respond to threats, bolstering overall security posture and incident response capabilities.
- Resource Optimization: Optimizing log data usage enhances scalability and performance within the Sentinel environment, ensuring efficient utilization of computing resources.
In summary, log trimming in Microsoft Sentinel contributes to cost savings, operational efficiency, enhanced security analysis, and resource optimization, thereby strengthening security monitoring capabilities.


## How transformations work

![Image](https://secbyte.in/wp-content/uploads/2024/02/10.png?w=999)
In Azure Monitor, transformations occur after data is collected but before it reaches its final destination. These transformations, defined in data collection rules (DCRs) using Kusto Query Language (KQL), fine-tune each piece of incoming data to fit its destination’s requirements. For instance, when gathering data from a virtual machine via Azure Monitor Agent, we can specify what data to collect and apply additional filters or create new data points before sending it off. This ensures that the data is well-prepared for analysis downstream, making the monitoring process more effective and insightful.


## Steps for Log Trimming through Data Collection Transformations in Azure Monitor

![Image](https://secbyte.in/wp-content/uploads/2024/02/1.png?w=1024)
2. Navigate to the Azure Monitor service.


![Image](https://secbyte.in/wp-content/uploads/2024/02/2.png?w=1024)
3. Locate the “Data Collection Rule” section within Azure Monitor.


![Image](https://secbyte.in/wp-content/uploads/2024/02/3.png?w=1024)
4. Select the desired DCR name you want for transformation.

5. Once you are inside DCR section, select “Export template” in the automation segment.


![Image](https://secbyte.in/wp-content/uploads/2024/02/4.png?w=1024)
6. Choose the “Deploy” option to proceed.


![Image](https://secbyte.in/wp-content/uploads/2024/02/6.png?w=1024)
7. Click on “Edit template” to access the JSON template.


![Image](https://secbyte.in/wp-content/uploads/2024/02/7.png?w=1024)
8. In the json template, add the desired KQL transformation at the bottom under the “transformkql” section as shown below.


![Image](https://secbyte.in/wp-content/uploads/2024/02/8.png?w=1024)
9. Save the changes and proceed to the “review + create” section.

10. Click on the “Create” button to finalize the transformation.

By following these steps, organizations can effectively implement log trimming to streamline log data and enhance their security monitoring capabilities in Microsoft Sentinel.


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
