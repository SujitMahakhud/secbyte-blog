---
title: "Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table"
date: "2024-05-24"
author: "Sujit Mahakhud"
categories: ['Cyber Security', 'incident management', 'microsoft-sentinel']
tags: ['Azure Sentinel', 'Cloud Security', 'kql', 'kqlquery', 'microsoft-sentinel', 'query', 'Securityincident']
description: "Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table - Comprehensive guide for cybersecurity professionals."
slug: "why-incident-counts-differ-in-microsoft-sentinel-portal-vs-securityincident-table"
original_url: "https://secbyte.in/2024/05/24/why-incident-counts-differ-in-microsoft-sentinel-portal-vs-securityincident-table/"
seo_title: "Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table | SecByte"
seo_description: "Learn about Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table with this detailed guide from SecByte."
keywords: ['azure sentinel', 'cloud security', 'kql', 'kqlquery', 'microsoft-sentinel']
---

# Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table

Have you ever noticed a discrepancy between the incident counts in the Microsoft Sentinel Incident portal and the SecurityIncident table? You’re not alone! Many users find this difference puzzling, especially when trying to reconcile data over a fixed time period. Typically, the SecurityIncident table displays a higher incident count compared to the Incident portal. But why does this happen?

This difference occurs because the SecurityIncident table includes all security alerts associated with a specific security incident without grouping them into single incidents. On the other hand, the Sentinel Incident portal consolidates these alerts, presenting a lower, more streamlined count.

Let’s dive into the actual query behind the SecurityIncident table to understand this better.


## The Actual Query Behind the Incident Count

To clarify the difference, here is the query used for the count in the SecurityIncident table:


``````


### Breaking Down the Query

- Summarize Incident Count:


``````

This part of the query groups the incidents by their number, alert IDs, title, and severity, and counts the number of incidents.

- Extract Alerts:


``````


``````

- Expand Alerts:


``````


``````

- Join with Security Alerts:


``````

This part of the query joins the SecurityIncident data with the SecurityAlert data based on the alert IDs. It summarizes the alert count by alert severity, system alert ID, and alert name.

- Summarize Final Results:


``````

Finally, the query summarizes the total alert count and creates a set of alert names for each incident number, title, and severity.


### Conclusion

The key takeaway is that the SecurityIncident table includes all security alerts associated with incidents, leading to a higher count. In contrast, the Sentinel Incident portal groups these alerts into single incidents, resulting in a lower count. Understanding this query helps explain the reason behind the difference in incident counts.

By grasping this concept, you can better interpret your incident data and make more informed decisions about your security posture.

Sujit Mahakhud


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

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2024/05/24/why-incident-counts-differ-in-microsoft-sentinel-portal-vs-securityincident-table/)

---

*Last Updated: 2024-05-24*  
*Author: Sujit Mahakhud | Cybersecurity Specialist*
