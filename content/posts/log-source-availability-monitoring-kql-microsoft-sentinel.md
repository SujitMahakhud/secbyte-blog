---
title: "Log Source Availability Monitoring with KQL in Microsoft Sentinel"
date: "2024-09-16"
author: "Sujit Mahakhud"
categories: ["Microsoft Sentinel", "KQL", "SOC", "Data Management", "Cyber Security"]
tags: ["microsoft sentinel", "kql", "log monitoring", "data freshness", "soc operations", "kusto query language", "siem", "security operations"]
description: "Essential KQL query for SOC teams to monitor data freshness and detect potential delays in security log ingestion across multiple data sources in Microsoft Sentinel."
slug: "log-source-availability-monitoring-kql-microsoft-sentinel"
original_url: "https://secbyte.in/2024/09/16/log-source-availability-monitoring-with-kql-in-microsoft-sentinel-an-essential-query-for-soc-teams/"
seo_title: "Log Source Availability Monitoring with KQL - Essential Query for SOC Teams 2024"
seo_description: "Monitor security log freshness in Microsoft Sentinel with this comprehensive KQL query. Detect ingestion delays across AAD, Syslog, Defender, and more."
keywords: ["kql log monitoring", "microsoft sentinel data freshness", "soc log monitoring", "sentinel query", "log ingestion monitoring", "kql query soc", "sentinel availability monitoring"]
---

# Log Source Availability Monitoring with KQL in Microsoft Sentinel: An Essential Query for SOC Teams

## Introduction

In a Security Operations Center (SOC), timely and accurate data is crucial for identifying and responding to threats. Delays in receiving security logs can lead to gaps in monitoring and create blind spots that adversaries may exploit. Ensuring that logs are being ingested at the expected frequency is critical for maintaining a proactive defense posture.

In this blog, we will explore a KQL (Kusto Query Language) query designed to monitor data freshness and detect potential delays in security logs across multiple data sources in Microsoft Sentinel. This query helps SOC teams identify gaps in log ingestion, allowing them to take action before missing data leads to missed threats.

---

## Understanding the KQL Query

The KQL query below is structured to check for delays across various log tables. It compares the current time with the last time logs were ingested from specific data sources, and it flags those exceeding a predefined threshold.

```kql
let thresholdExceeded = (tablename: string, TimeDifference: long) {
    case(
        tablename == "AADProvisioningLogs", TimeDifference > 300,
        tablename == "AuditLogs", TimeDifference > 30,
        tablename == "AADUserRiskEvents", TimeDifference > 300,
        tablename == "SigninLogs", TimeDifference > 30,
        tablename == "DeviceLogonEvents", TimeDifference > 30,
        tablename == "SecurityIncident", TimeDifference > 30,
        tablename == "Syslog", TimeDifference > 30,
        tablename == "CommonSecurityLog", TimeDifference > 15,
        tablename == "Operation", TimeDifference > 30,
        tablename == "UserPeerAnalytics", TimeDifference > 1440,
        tablename == "Usage", TimeDifference > 1440,
        tablename == "AADRiskyUsers", TimeDifference > 300,
        tablename == "Anomalies", TimeDifference > 720,
        tablename == "IdentityDirectoryEvents", TimeDifference > 30,
        tablename == "IdentityQueryEvents", TimeDifference > 30,
        tablename == "SentinelHealth", TimeDifference > 1440,
        tablename == "EmailPostDeliveryEvents", TimeDifference > 200,
        tablename == "AzureActivity", TimeDifference > 30,
        tablename == "AlertInfo", TimeDifference > 30,
        tablename == "IdentityLogonEvents", TimeDifference > 30,
        tablename == "EmailEvents", TimeDifference > 30,
        tablename == "EmailAttachmentInfo", TimeDifference > 30,
        tablename == "AlertEvidence", TimeDifference > 30,
        tablename == "Heartbeat", TimeDifference > 15,
        tablename == "EmailUrlInfo", TimeDifference > 30,
        tablename == "UrlClickEvents", TimeDifference > 30,
        tablename == "SentinelAudit", TimeDifference > 30,
        tablename == "CloudAppEvents", TimeDifference > 30,
        tablename == "AADServicePrincipalSignInLogs", TimeDifference > 300,
        tablename == "IdentityInfo", TimeDifference > 30,
        tablename == "SecurityAlert", TimeDifference > 30,
        tablename == "DeviceNetworkInfo", TimeDifference > 30,
        tablename == "ThreatIntelligenceIndicator", TimeDifference > 1440,
        tablename == "AADManagedIdentitySignInLogs", TimeDifference > 30,
        tablename == "AzureMetrics", TimeDifference > 30,
        tablename == "DeviceProcessEvents", TimeDifference > 30,
        tablename == "DeviceInfo", TimeDifference > 30,
        tablename == "DeviceRegistryEvents", TimeDifference > 30,
        tablename == "AADNonInteractiveUserSignInLogs", TimeDifference > 300,
        tablename == "SecurityEvent", TimeDifference > 30,
        tablename == "OfficeActivity", TimeDifference > 30,
        tablename == "DeviceFileCertificateInfo", TimeDifference > 30,
        tablename == "DeviceEvents", TimeDifference > 30,
        tablename == "MicrosoftGraphActivityLogs", TimeDifference > 30,
        tablename == "BehaviorAnalytics", TimeDifference > 30,
        tablename == "DeviceNetworkEvents", TimeDifference > 30,
        tablename == "DeviceFileEvents", TimeDifference > 30,
        tablename == "DeviceImageLoadEvents", TimeDifference > 30,
        tablename == "AzureDiagnostics", TimeDifference > 30,
        tablename == "CrowdStrike", TimeDifference > 720,
        false
    )
};
let table = union isfuzzy=true withsource=tablename *
| summarize LastSeen = max(TimeGenerated) by tablename;
let crowdstrike = CrowdstrikeFalconEventStream
| extend tablename = "CrowdStrike"
| summarize LastSeen = max(TimeGenerated) by tablename;
let allData = union isfuzzy=true crowdstrike, table
| extend TimeDifference = datetime_diff("minute", now(), LastSeen)
| extend ThresholdExceeded = thresholdExceeded(tablename, TimeDifference);
let commonSecurityLogData = CommonSecurityLog
| summarize arg_max(TimeGenerated, *) by Computer
| extend tablename = Computer
| summarize LastSeen = max(TimeGenerated) by tablename
| extend TimeDifference = datetime_diff("minute", now(), LastSeen)
| extend ThresholdExceeded = iff(TimeDifference > 60, true, false);
union isfuzzy=true allData, commonSecurityLogData
| where ThresholdExceeded == true
| project-away ThresholdExceeded
```

---

## How This Query Helps

This query allows SOC teams to **monitor data freshness across various log sources** and ensure logs are being ingested as expected. It works by comparing the current time to the `TimeGenerated` field of each log source and flags logs that haven't been ingested within a specific time threshold.

### Key Benefits:

- ✅ **Quickly Identifying Delays**: Pinpoint which log sources have delayed data ingestion for immediate investigation
- ✅ **Customizable Thresholds**: Set custom thresholds based on specific environment needs
- ✅ **Comprehensive Coverage**: Monitors multiple data sources from Syslog and Audit Logs to CrowdStrike Falcon events

---

## Why Is This Important?

Monitoring log freshness is vital for maintaining effective security monitoring. **Delayed logs mean delayed threat detection**, which can leave your organization vulnerable to attacks.

### Critical Reasons:

| Reason | Impact |
|--------|--------|
| **Incident Response** | Timely logs are essential for detecting and responding to incidents quickly |
| **Data Accuracy** | Real-time data ensures accuracy of threat intelligence and detection mechanisms |
| **Compliance** | Many regulations require timely monitoring of security events |
| **Threat Detection** | Missing or delayed logs result in missed alerts and longer dwell times |

---

## Query Breakdown

### 1. **Threshold Function**

The `thresholdExceeded` function defines acceptable delays for each log type:

- **High-frequency logs** (SigninLogs, Syslog): 30 minutes
- **Critical logs** (CommonSecurityLog, Heartbeat): 15 minutes  
- **Low-frequency logs** (ThreatIntelligenceIndicator): 1440 minutes (24 hours)

### 2. **Data Collection**

The query uses `union isfuzzy=true` to collect data from all available tables and calculates the time difference between now and the last log entry.

### 3. **Filtering Results**

Only log sources exceeding their threshold are displayed, allowing SOC teams to focus on actionable issues.

---

## Implementation Guide

### Step 1: Deploy the Query

1. Navigate to **Microsoft Sentinel** → **Logs**
2. Paste the KQL query
3. Click **Run** to test

### Step 2: Create an Analytics Rule

1. Go to **Analytics** → **Create** → **Scheduled query rule**
2. Set the query to run every 15-30 minutes
3. Configure alert severity based on critical log sources

### Step 3: Configure Automation

Create a **Playbook (Logic App)** to:
- Send notifications to SOC team
- Create ServiceNow/Jira tickets
- Trigger diagnostic scripts

---

## Customization Tips

### Adjust Thresholds

Modify threshold values based on your environment:

```kql
tablename == "Syslog", TimeDifference > 60,  // Changed from 30 to 60 minutes
```

### Add New Data Sources

Add custom tables to the threshold function:

```kql
tablename == "CustomSecurityLog", TimeDifference > 45,
```

### Filter by Priority

Add a priority field to focus on critical sources:

```kql
| extend Priority = case(
    tablename in ("SecurityAlert", "SecurityIncident"), "High",
    tablename in ("Syslog", "SecurityEvent"), "Medium",
    "Low"
)
| where Priority in ("High", "Medium")
```

---

## Best Practices

### 1. **Regular Review**
- Review thresholds monthly based on log patterns
- Adjust for business hours vs. off-hours

### 2. **Alert Tuning**
- Start with conservative thresholds
- Reduce false positives by analyzing patterns

### 3. **Documentation**
- Document baseline ingestion times
- Maintain runbooks for common delay scenarios

### 4. **Integration**
- Integrate with ITSM platforms
- Create dashboards for visualization

---

## Troubleshooting Common Issues

### Issue: Too Many False Positives

**Solution**: Increase threshold values for noisy sources or exclude test environments.

### Issue: Missing Data Sources

**Solution**: Verify data connector configuration and ensure data is flowing to Sentinel.

### Issue: Query Performance

**Solution**: Optimize by limiting time range or specific tables:

```kql
let table = union isfuzzy=true withsource=tablename SecurityEvent, Syslog, SigninLogs
| where TimeGenerated > ago(1h)
| summarize LastSeen = max(TimeGenerated) by tablename;
```

---

## Conclusion

This KQL query provides an efficient way for SOC teams to monitor the freshness of their security logs in Microsoft Sentinel. By detecting potential delays across multiple data sources, it helps ensure that your security monitoring is timely and effective.

Whether you are managing a large number of data streams or just a few, proactively monitoring data ingestion is key to maintaining a robust security posture.

**Implement this query today** to gain better visibility into your log ingestion pipeline and ensure your SOC is ready to respond to threats as they arise.

---

## Additional Resources

- [Microsoft Sentinel Documentation](https://learn.microsoft.com/azure/sentinel/)
- [KQL Quick Reference](https://learn.microsoft.com/azure/data-explorer/kql-quick-reference)
- [SOC Operations Best Practices](https://learn.microsoft.com/security/operations/)

---

## 💬 Got Questions?

I'm here to help! Feel free to:

- **Leave a comment** below with your questions
- **Connect on LinkedIn**: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- **Subscribe on YouTube**: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- **Email me**: sujitmahakhud@gmail.com

I also offer **one-on-one training** on:
- Microsoft Sentinel
- KQL (Kusto Query Language)
- SOC Operations & Automation
- Azure Security

---

🧩 **Read this article on the official website**: [SecByte.in](https://secbyte.in/2024/09/16/log-source-availability-monitoring-with-kql-in-microsoft-sentinel-an-essential-query-for-soc-teams/)

---

*Last Updated: September 16, 2024*  
*Author: Sujit Mahakhud | SOC Lead & Microsoft Sentinel Specialist*
