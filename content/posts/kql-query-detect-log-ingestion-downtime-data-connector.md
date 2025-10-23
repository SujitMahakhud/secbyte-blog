---
title: "KQL Query to Detect Log Ingestion Downtime by Data Connector/Tables in Microsoft Sentinel"
date: "2024-03-15"
author: "Sujit Mahakhud"
categories: ["Cyber Security"]
tags: ["azure-sentinel", "data-integration", "log-downtime", "microsoft-sentinel", "siem", "kql", "monitoring"]
description: "Learn how to detect log ingestion downtime in Microsoft Sentinel using a comprehensive KQL query. Monitor multiple data connectors, identify delays, and ensure timely threat detection with customizable alerting thresholds."
slug: "kql-query-detect-log-ingestion-downtime-data-connector"
original_url: "https://secbyte.in/2024/03/15/kql-query-to-detect-log-ingestion-downtime-by-data-connector-tables-in-microsoft-sentinel/"
seo_title: "Detect Log Ingestion Downtime in Microsoft Sentinel with KQL | SecByte"
seo_description: "Comprehensive guide to monitoring log ingestion in Microsoft Sentinel. Use this customizable KQL query to detect delays across multiple data connectors and ensure continuous security monitoring."
keywords: ["microsoft sentinel kql", "log ingestion monitoring", "sentinel downtime detection", "data connector monitoring", "kql query sentinel", "log ingestion delay", "sentinel data connectors", "siem monitoring"]
---

# 🔍 KQL Query to Detect Log Ingestion Downtime by Data Connector/Tables in Microsoft Sentinel

## 📋 Introduction

In a cybersecurity environment, timely detection of anomalies or issues in log ingestion is critical for maintaining the integrity and effectiveness of your security operations. Microsoft Sentinel offers a powerful platform for managing and analyzing security logs, but it's equally important to have mechanisms in place to detect and address any downtime or delays in log ingestion. In this blog post, we'll explore a Key Query Language (KQL) query designed to help you detect log ingestion downtime by data connectors and their respective tables in Microsoft Sentinel.

## 💡 Value Proposition

The query is a must-have for all Microsoft Sentinel implementations. It addresses the critical need for timely detection of log ingestion issues, ensuring logs from essential sources are ingested promptly. With its comprehensive monitoring and proactive alerting capabilities, it's indispensable for efficiently managing security risks and responding swiftly to threats.

### Key Benefits:

- ⏱️ **Timely Detection**: Quickly identifies if logs from critical sources like Windows Security Events or Syslog are delayed
- 📊 **Comprehensive Monitoring**: Monitors multiple data connectors for a complete view of log ingestion status
- ⚙️ **Customizable and Scalable**: Can be easily adjusted to monitor new data connectors and adapt to changing needs

**Note:** The below KQL query serves as a foundational template for monitoring log ingestion in Microsoft Sentinel. However, it must be customized according to the specific data connectors available in your environment. Adjustments may be necessary to include relevant data sources and tailor alerting thresholds to align with your organization's security requirements and infrastructure setup.

## 🔧 KQL Query to Detect Log Ingestion Downtime by Data Connector

```kql
let WindowsSecurityEventsviaAMA = SecurityEvent
| where isnotempty(TimeGenerated)
| extend tablename= "securityevent"
| summarize LastSeen= max(TimeGenerated)by tablename;

let syslog = Syslog
| where isnotempty(TimeGenerated)
| extend tablename= "syslog"
| summarize LastSeen= max(TimeGenerated)by tablename;

let SonicWall = CommonSecurityLog
| where DeviceVendor == "SonicWall"
| extend tablename= "SonicWall"
| summarize LastSeen= max(TimeGenerated)by tablename;

let SemperisDirectoryServicesProtector = dsp_parser
| where isnotempty(TimeGenerated)
| extend tablename= "dsp_parser"
| summarize LastSeen= max(TimeGenerated)by tablename;

let PaloAltoNetworks = CommonSecurityLog
| where DeviceVendor == "Palo Alto Networks" and DeviceProduct has "PAN-OS"
| extend tablename= "PaloAltoNetworks"
| summarize LastSeen= max(TimeGenerated)by tablename;

let EntraIDProtection = SecurityAlert
| where ProductName == "Azure Active Directory Identity Protection" and ProviderName == "IPC"
| extend tablename= "SecurityAlert (IPC)"
| summarize LastSeen= max(TimeGenerated)by tablename;

let securityalert = (datatype: string, name: string) {
SecurityAlert
| where ProviderName == name or ProductName == name
| extend tablename= datatype
| summarize LastSeen= max(TimeGenerated)by tablename
};

let crowdstrikefalcon = (datatype: string) {
table(datatype)
| where isnotempty(TimeGenerated)
| extend tablename= datatype
| summarize LastSeen= max(TimeGenerated)by tablename
};

let CrowdStrikeEndpointProtection= CommonSecurityLog
| where DeviceProduct == "FalconHost"
| extend tablename= "CrowdStrikeEndpointProtection"
| summarize LastSeen= max(TimeGenerated)by tablename;

let InfobloxNIOS= Infoblox
| extend tablename= "Infoblox"
| summarize LastSeen= max(TimeGenerated)by tablename;

let CitrixADC= CitrixADCEvent
| extend tablename= "CitrixADCEvent"
| summarize LastSeen= max(TimeGenerated)by tablename;

let ciscomeraki= CiscoMeraki
| extend tablename= "meraki_CL"
| summarize LastSeen= max(TimeGenerated)by tablename;

let table= union withsource=tablename *
| summarize LastSeen = max(TimeGenerated) by tablename, ProcessName, SourceSystem
| extend EntraID = iff(tablename in ("SigninLogs", "AuditLogs", "AADNonInteractiveUserSignInLogs", "AADRiskyServicePrincipals", "NetworkAccessTraffic", "AADServicePrincipalRiskEvents", "ADFSSignInLogs", "AADProvisioningLogs", "AADProvisioningLogs", "AADManagedIdentitySignInLogs", "AADUserRiskEvents", "AADUserRiskEvents"), "Microsoft_EntraID", "")
| extend CiscoISE = iff(ProcessName has_any ("CSCO", "CISE"), "Cisco Identity Services Engine", "")
| extend AmazonWebServicesS3 = iff(tablename in ("AWSCloudWatch", "AWSCloudTrail", "AWSGuardDuty", "AWSVPCFlow"), "Amazon Web Services S3 (Preview)", "")
// Additional data connector mappings...
| extend DefenderXDR = iff(tablename in ("SecurityIncident", "SecurityAlert", "DeviceInfo", "DeviceNetworkInfo", "IdentityLogonEvents", "IdentityQueryEvents", "IdentityDirectoryEvents", "CloudAppEvents", "AlertInfo", "AlertEvidence", "DeviceEvents", "DeviceFileEvents", "DeviceImageLoadEvents", "DeviceLogonEvents", "DeviceNetworkEvents", "DeviceProcessEvents", "DeviceRegistryEvents", "DeviceFileCertificateInfo", "EmailEvents", "EmailUrlInfo", "EmailAttachmentInfo", "EmailPostDeliveryEvents", "UrlClickEvents"), "Microsoft Defender XDR", "");

// Union all data sources
union
ciscomeraki,
table,
CitrixADC,
InfobloxNIOS,
CrowdStrikeEndpointProtection,
PaloAltoNetworks,
SemperisDirectoryServicesProtector,
SonicWall,
syslog,
WindowsSecurityEventsviaAMA
| extend ['Data Connector'] = coalesce(EntraID, CiscoISE, AmazonWebServicesS3, DefenderXDR, /* ... other connectors ... */)
| where isnotempty(['Data Connector'])
| extend TimeDifference = datetime_diff("minute", now(), LastSeen)
| summarize LastSeen =max(LastSeen)by ['Data Connector'], ['Table Name']= tablename, TimeDifference
| project-reorder ['Data Connector'], ['Table Name'], LastSeen, TimeDifference
| sort by TimeDifference desc
```

### 📈 Query Results

The query will provide results showing:
- **Data Connector**: Name of the data connector
- **Table Name**: Associated table in Microsoft Sentinel
- **LastSeen**: Timestamp of the last log ingested
- **TimeDifference**: Time elapsed (in minutes) since the last log

## 📊 Visualization

The above KQL query can be leveraged to visualize the log downtime through workbooks and dashboards.

Visualizing the log ingestion status through a workbook provides a clear and intuitive way to monitor the health of data connectors in Microsoft Sentinel. It offers a quick overview of the last seen times for each data connector, highlighting any potential delays or issues in log ingestion.

### Benefits of Visualization:
- 🎯 **At-a-Glance Status**: Quickly identify data connectors with delays
- 📉 **Trend Analysis**: Track log ingestion patterns over time
- 🚨 **Proactive Alerting**: Set up alerts based on threshold breaches
- 📋 **Reporting**: Generate reports for stakeholders and compliance

## 🎯 Conclusion

In summary, the KQL query offers a solid foundation for monitoring log ingestion in Microsoft Sentinel, providing vital insights into data connector status. When combined with visual workbooks, these insights are enhanced, offering clear and intuitive representations of log ingestion health. Together, they empower organizations to proactively manage security operations, ensuring timely detection and resolution of any issues, thus bolstering overall cybersecurity defenses.

---

## 📞 Contact Information

**Follow us on LinkedIn:** [www.linkedin.com/in/sujitmahakhud](http://www.linkedin.com/in/sujitmahakhud)

**Email:** www.sujitmahakhud@gmail.com

**YouTube:** [https://www.youtube.com/@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)

---

📚 **Read this article on the official website:** [SecByte.in](https://secbyte.in/2024/03/15/kql-query-to-detect-log-ingestion-downtime-by-data-connector-tables-in-microsoft-sentinel/)
