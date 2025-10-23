---
title: "Mastering Sentinel: The Essential KQL Query for Every SOC Team"
date: "2024-08-18"
author: "Sujit Mahakhud"
categories: ["Microsoft Sentinel", "KQL", "SOC Operations", "Incident Management", "Cyber Security"]
tags: ["microsoft sentinel", "kql", "soc", "incident management", "security analytics", "cybersecurity", "threat detection", "siem"]
description: "Essential KQL query for SOC teams to analyze incidents, track response times, and optimize security operations in Microsoft Sentinel."
slug: "mastering-sentinel-essential-kql-query-soc-team"
original_url: "https://secbyte.in/2024/08/18/mastering-sentinel-the-essential-kql-query-for-every-soc-team/"
seo_title: "Mastering Sentinel: Essential KQL Query for SOC Teams 2024"
seo_description: "Must-have KQL query for analyzing security incidents in Microsoft Sentinel. Track incident trends, response times, and tool effectiveness for SOC excellence."
keywords: ["kql query sentinel", "soc kql query", "microsoft sentinel incidents", "security analytics kql", "incident response query", "sentinel soc operations"]
---

# Mastering Sentinel: The Essential KQL Query for Every SOC Team

## Introduction

In the ever-evolving landscape of cybersecurity, Security Operations Center (SOC) teams play a crucial role in defending organizations against a multitude of threats. To efficiently manage and analyze incidents, having the right tools and queries at your disposal is imperative. One such tool is the powerful Kusto Query Language (KQL) used in Microsoft Sentinel. Today, I'll be walking you through a must-have KQL query that every SOC team and client should have in their toolkit.

---

## The Query That Delivers Insights

Here's the complete KQL query:

```kql
SecurityIncident
| where TimeGenerated >= ago(24h)
| extend ProductName = tostring(parse_json(tostring(AdditionalData.alertProductNames))[0])
| summarize arg_max(TimeGenerated, *) by IncidentNumber
| extend IncidentDuration = iif(Status == "Closed", datetime_diff('minute', ClosedTime, CreatedTime), datetime_diff('minute', now(), CreatedTime))
| summarize IncidentCount = count()
by
IncidentNumber,
tostring(AlertIds),
TimeGenerated,
Title,
Severity,
Status,
IncidentDuration,
ProviderName,
ProductName
| extend Alerts = extract("\\[(.*?)\\]", 1, tostring(AlertIds))
| mv-expand todynamic(AlertIds) to typeof(string)
| join (
    SecurityAlert
    | summarize AlertCount = count() by AlertSeverity, SystemAlertId, AlertName, Status
)
on $left.AlertIds == $right.SystemAlertId
| summarize Alert_Count=sum(AlertCount), make_set(AlertName)
by
IncidentNumber,
Title,
Severity,
Status,
IncidentDuration,
ProviderName,
TimeGenerated,
ProductName
| extend ["Alert Name"] = tostring(set_AlertName[0])
| summarize
    TotalIncidents = count(),
    FirstIncidentTime= min(TimeGenerated),
    LastIncidentTime=max(TimeGenerated),
    ClosedIncidents = countif(Status == "Closed"),
    ActiveIncidents = countif(Status == "Active"),
    NewIncidents = countif(Status == "New"),
    AvgIncidentDuration = avg(IncidentDuration),
    MaxIncidentDuration = max(IncidentDuration),
    MinIncidentDuration = min(IncidentDuration),
    SeverityDistribution = make_list(Severity)
by ProviderName, Title, ProductName
| project
    FirstIncidentTime,
    LastIncidentTime,
    ProviderName,
    ProductName,
    Title,
    TotalIncidents,
    ClosedIncidents,
    ClosedPercentage=strcat(round(ClosedIncidents * 100.0 / TotalIncidents, 1), "%"),
    ActiveIncidents,
    ActivePercentage=strcat(round(ActiveIncidents * 100.0 / TotalIncidents, 1), "%"),
    NewIncidents,
    NewPercentage=strcat(round(NewIncidents * 100.0 / TotalIncidents, 1), "%"),
    round(AvgIncidentDuration, 2),
    MaxIncidentDuration,
    MinIncidentDuration,
    SeverityDistribution
| sort by TotalIncidents desc
```

---

## Step-by-Step Explanation

### 1. Filtering Incidents

The query starts by filtering security incidents that occurred within the last 24 hours:

```kql
| where TimeGenerated >= ago(24h)
```

This ensures that your SOC team is focusing on the most recent and potentially critical incidents.

### 2. Extracting Product Information

The `extend` operator extracts the name of the security product associated with each incident:

```kql
| extend ProductName = tostring(parse_json(tostring(AdditionalData.alertProductNames))[0])
```

This is crucial for understanding which tools and technologies are generating the most alerts, enabling better resource allocation and tool optimization.

### 3. Identifying Key Incidents

```kql
| summarize arg_max(TimeGenerated, *) by IncidentNumber
```

Ensures you are working with the most up-to-date information for each incident by selecting the most recent record.

### 4. Calculating Incident Duration

```kql
| extend IncidentDuration = iif(Status == "Closed", datetime_diff('minute', ClosedTime, CreatedTime), datetime_diff('minute', now(), CreatedTime))
```

Calculates the duration of each incident, whether it's closed or still active. This metric is vital for:
- Understanding response times
- Identifying bottlenecks in SOC processes
- Measuring team efficiency

### 5. Aggregating Alerts

Through the `join` and `mv-expand` operators, the query connects incident data with corresponding alerts:

```kql
| join (
    SecurityAlert
    | summarize AlertCount = count() by AlertSeverity, SystemAlertId, AlertName, Status
)
on $left.AlertIds == $right.SystemAlertId
```

This provides a holistic view of alerts tied to each incident, useful for understanding severity and nature.

### 6. Comprehensive Summary

The query provides a detailed summary including:
- **Total, closed, active, and new incident counts**
- **Average, maximum, and minimum incident duration**
- **Severity distribution**
- **Percentage breakdowns** for each status

### 7. Clear Presentation

Final steps ensure results are organized and highlight the most incident-prone areas:

```kql
| sort by TotalIncidents desc
```

---

## Why This Query is a Game-Changer

This KQL query is a cornerstone for SOC teams striving for operational excellence.

### Key Benefits:

| Benefit | Description |
|---------|-------------|
| **Prioritize Effectively** | Focus on critical incidents and alerts, directing resources where needed most |
| **Enhance Reporting** | Generate detailed reports on incident trends, response times, and tool effectiveness |
| **Optimize Operations** | Identify patterns in incident duration and alert severity for continuous improvement |
| **Stakeholder Transparency** | Share comprehensive metrics with management and stakeholders |
| **Performance Metrics** | Track SOC team efficiency and response effectiveness |

---

## Practical Use Cases

### 1. Daily SOC Briefings

Run this query every morning to provide team briefings on:
- Overnight incidents
- Trending security issues
- Response time performance

### 2. Weekly Management Reports

Generate executive summaries showing:
- Incident closure rates
- Average response times
- Security tool effectiveness

### 3. Performance Analysis

Analyze trends over time to:
- Identify improvement areas
- Justify resource requests
- Optimize incident response workflows

### 4. Tool Evaluation

Assess security product effectiveness by:
- Comparing incident volumes by product
- Analyzing false positive rates
- Identifying integration gaps

---

## Customization Options

### Adjust Time Range

```kql
| where TimeGenerated >= ago(7d)  // Last 7 days
| where TimeGenerated >= ago(30d) // Last 30 days
```

### Filter by Severity

```kql
| where Severity in ("High", "Critical")
```

### Filter by Product

```kql
| where ProductName has "Defender"
```

### Add Team Assignment

```kql
| extend AssignedTo = Owner.assignedTo
| summarize ... by AssignedTo
```

---

## Integration Tips

### 1. Scheduled Analytics Rule

Create a scheduled rule to run this query daily and generate reports automatically.

### 2. Dashboard Visualization

Add query results to Azure dashboards for real-time monitoring.

### 3. Logic App Automation

Trigger Logic Apps to:
- Send email reports to management
- Post results to Teams/Slack
- Create tickets for high-severity trends

### 4. Power BI Integration

Export query results to Power BI for advanced visualizations and trend analysis.

---

## Best Practices

### 1. Regular Review
- Run query at consistent intervals
- Compare results week-over-week
- Document significant changes

### 2. Alert Tuning
- Use insights to tune detection rules
- Reduce false positives
- Improve signal-to-noise ratio

### 3. Team Training
- Ensure all SOC analysts understand the query
- Train on interpreting results
- Share insights across shifts

### 4. Documentation
- Document baseline metrics
- Track improvement initiatives
- Maintain historical trend data

---

## Troubleshooting

### Issue: Query Timeout

**Solution**: Reduce time range or add filters:
```kql
| where TimeGenerated >= ago(24h)
| where ProviderName == "Azure Sentinel"
```

### Issue: Missing Product Names

**Solution**: Check AdditionalData field structure:
```kql
| extend AdditionalDataRaw = AdditionalData
| project AdditionalDataRaw
```

### Issue: Duplicate Incidents

**Solution**: Verify `arg_max` is properly deduplicating:
```kql
| summarize arg_max(TimeGenerated, *) by IncidentNumber
```

---

## Performance Optimization

### 1. Use Time Filters Early

Place time filters at the beginning of the query for better performance.

### 2. Limit Join Scope

```kql
| join kind=inner (
    SecurityAlert
    | where TimeGenerated >= ago(24h)
    ...
)
```

### 3. Project Only Needed Fields

Reduce data processed by projecting only required columns early in the query.

---

## Conclusion

In the world of cybersecurity, where every second counts, having the right KQL query at your fingertips can make all the difference. This essential query in Microsoft Sentinel not only empowers SOC teams to stay ahead of threats but also equips them with the insights needed to drive continuous improvement.

Make this query a part of your regular operations and watch your SOC's effectiveness soar.

**Remember**: In cybersecurity, it's not just about reacting to threats—it's about anticipating them. With this query, your SOC team will be better prepared than ever.

---

## Additional Resources

- [Microsoft Sentinel Documentation](https://learn.microsoft.com/azure/sentinel/)
- [KQL Quick Reference](https://learn.microsoft.com/azure/data-explorer/kql-quick-reference)
- [Security Incident Schema](https://learn.microsoft.com/azure/sentinel/normalization-schema)

---

## 💬 Got Questions?

- **Connect on LinkedIn**: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- **Subscribe on YouTube**: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- **Email me**: sujitmahakhud@gmail.com

I offer **one-on-one training** on:
- Microsoft Sentinel
- KQL Query Development
- SOC Operations & Automation
- Incident Response Strategies

---

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2024/08/18/mastering-sentinel-the-essential-kql-query-for-every-soc-team/)

---

*Last Updated: August 18, 2024*  
*Author: Sujit Mahakhud | SOC Operations Specialist*
