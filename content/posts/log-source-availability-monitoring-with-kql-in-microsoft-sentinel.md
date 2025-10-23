---
title: "Log Source Availability Monitoring with KQL in Microsoft Sentinel: An Essential Query for SOC Teams"
date: "2024-09-16"
author: "Sujit Mahakhud"
tags: ["Azure Sentinel", "Cloud Security", "Cyber Security", "KQL", "Microsoft Sentinel", "SIEM"]
categories: ["Azure", "Azure Sentinel", "Cyber Security", "Data Management", "Incident Management", "Microsoft Security", "Microsoft Sentinel"]
original_url: "https://secbyte.in/2024/09/16/log-source-availability-monitoring-with-kql-in-microsoft-sentinel-an-essential-query-for-soc-teams/"
---

# Log Source Availability Monitoring with KQL in Microsoft Sentinel: An Essential Query for SOC Teams

In a Security Operations Center (SOC), timely and accurate data is crucial for identifying and responding to threats. Delays in receiving security logs can lead to gaps in monitoring and create blind spots that adversaries may exploit. Ensuring that logs are being ingested at the expected frequency is critical for maintaining a proactive defense posture.

In this blog, we will explore a KQL (Kusto Query Language) query designed to monitor data freshness and detect potential delays in security logs across multiple data sources in Microsoft Sentinel. This query helps SOC teams identify gaps in log ingestion, allowing them to take action before missing data leads to missed threats.

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

## How This Query Helps

This query allows SOC teams to **monitor data freshness across various log sources** and ensure logs are being ingested as expected. It works by comparing the current time to the `TimeGenerated` field of each log source and flags logs that haven't been ingested within a specific time threshold. If the data has been delayed beyond the set threshold for that log type, the query highlights it as an issue that requires attention.

Key benefits include:

- **Quickly Identifying Delays:** The query helps you pinpoint which log sources have delayed data ingestion, making it easier to investigate and resolve issues before they affect your monitoring
- **Customizable Thresholds:** Different log sources have varying expectations for how frequently logs should be ingested. This query allows you to set custom thresholds based on the specific needs of your environment, ensuring flexibility in how you monitor your data
- **Comprehensive Coverage:** The query covers multiple data sources, from common logs like Syslog and Audit Logs to more specialized data like CrowdStrike Falcon events. It ensures a wide-ranging view of your log ingestion status

## Why Is This Important?

Monitoring log freshness is vital for maintaining effective security monitoring in your environment. **Delayed logs mean delayed threat detection**, which can leave your organization vulnerable to attacks. By proactively monitoring for ingestion delays, you can ensure that your security controls are operating as expected and that you are receiving timely data to respond to threats.

Some key reasons why this is important:

- **Incident Response:** Timely logs are essential for detecting and responding to incidents quickly. Missing or delayed logs can result in missed alerts, giving attackers more time to exploit vulnerabilities
- **Data Accuracy:** Ingesting data within expected timeframes ensures the accuracy of your threat intelligence and detection mechanisms, allowing you to act based on real-time information
- **Compliance:** Many regulations require timely monitoring of security events. Delayed logs may lead to non-compliance and potential penalties

## Conclusion

This KQL query provides an efficient way for SOC teams to monitor the freshness of their security logs in Microsoft Sentinel. By detecting potential delays across multiple data sources, it helps ensure that your security monitoring is timely and effective. Whether you are managing a large number of data streams or just a few, proactively monitoring data ingestion is key to maintaining a robust security posture.

Implement this query today to gain better visibility into your log ingestion pipeline and ensure your SOC is ready to respond to threats as they arise.

---
🧩 Read this article on the official website: [secbyte.in](https://secbyte.in/2024/09/16/log-source-availability-monitoring-with-kql-in-microsoft-sentinel-an-essential-query-for-soc-teams/)
