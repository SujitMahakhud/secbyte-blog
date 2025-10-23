---
title: "Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table"
date: "2024-05-24"
author: "Sujit Mahakhud"
description: "Have you ever noticed a discrepancy between the incident counts in the Microsoft Sentinel Incident portal and the SecurityIncident table? You’re not a..."
slug: "why-incident-counts-differ-in-microsoft-sentinel-portal-vs-securityincident-table"
original_url: "https://secbyte.in/2024/05/24/why-incident-counts-differ-in-microsoft-sentinel-portal-vs-securityincident-table/"
---

# Why Incident Counts Differ in Microsoft Sentinel: Portal vs. SecurityIncident Table

Have you ever noticed a discrepancy between the incident counts in the Microsoft Sentinel Incident portal and the SecurityIncident table? You’re not alone! Many users find this difference puzzling, especially when trying to reconcile data over a fixed time period. Typically, the SecurityIncident table displays a higher incident count compared to the Incident portal. But why does this happen?

This difference occurs because the SecurityIncident table includes all security alerts associated with a specific security incident without grouping them into single incidents. On the other hand, the Sentinel Incident portal consolidates these alerts, presenting a lower, more streamlined count.

Let’s dive into the actual query behind the SecurityIncident table to understand this better.


## The Actual Query Behind the Incident Count
To clarify the difference, here is the query used for the count in the SecurityIncident table:


```kql
SecurityIncident
| summarize IncidentCount = count() by IncidentNumber, tostring(AlertIds), Title, Severity
| extend Alerts = extract("\\[(.*?)\\]", 1, tostring(AlertIds))
| mv-expand todynamic(AlertIds) to typeof(string)
| join 
(
    SecurityAlert
    | summarize AlertCount = count() by AlertSeverity, SystemAlertId, AlertName
) on $left.AlertIds == $right.SystemAlertId
| summarize sum(AlertCount), make_set(AlertName) by IncidentNumber, Title, Severity
```

### Breaking Down the Query

```kql
SecurityIncident
| summarize IncidentCount = count() by IncidentNumber, tostring(AlertIds), Title, Severity
```
This part of the query groups the incidents by their number, alert IDs, title, and severity, and counts the number of incidents.


```
| extend Alerts = extract("\\[(.*?)\\]", 1, tostring(AlertIds))
```
The extract function is used to pull out the alert IDs from the list of alerts associated with each incident.


```
| mv-expand todynamic(AlertIds) to typeof(string)
```
The mv-expand function expands the dynamic array of alert IDs into individual rows.


```kql
| join 
(
    SecurityAlert
    | summarize AlertCount = count() by AlertSeverity, SystemAlertId, AlertName
) on $left.AlertIds == $right.SystemAlertId
```
This part of the query joins the SecurityIncident data with the SecurityAlert data based on the alert IDs. It summarizes the alert count by alert severity, system alert ID, and alert name.


```kql
| summarize sum(AlertCount), make_set(AlertName) by IncidentNumber, Title, Severity
```
Finally, the query summarizes the total alert count and creates a set of alert names for each incident number, title, and severity.


### Conclusion
The key takeaway is that the SecurityIncident table includes all security alerts associated with incidents, leading to a higher count. In contrast, the Sentinel Incident portal groups these alerts into single incidents, resulting in a lower count. Understanding this query helps explain the reason behind the difference in incident counts.

By grasping this concept, you can better interpret your incident data and make more informed decisions about your security posture.

Sujit Mahakhud


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
