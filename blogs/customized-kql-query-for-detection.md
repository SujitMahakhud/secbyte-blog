---
title: "Customized KQL Query for Detection"
date: "2024-02-19"
author: "Sujit Mahakhud"
description: "In this blog post, I’ll walk you through a series of customized KQL queries that I’ve personally developed and refined. These queries are not only rea..."
slug: "customized-kql-query-for-detection"
original_url: "https://secbyte.in/2024/02/19/customized-kql-query-for-detection/"
---

# Customized KQL Query for Detection

In this blog post, I’ll walk you through a series of customized KQL queries that I’ve personally developed and refined. These queries are not only ready to use but also easily deployable with minor adjustments. Whether you’re a seasoned Security analyst or just dipping your toes into the world of KQL, these queries will help streamline your Security analysis process and unlock valuable insights.


## What You’ll Get from This Blog Post
- Understanding KQL Queries: Learn about KQL queries and how they can help analyze data.
- Clear Explanations: Each query comes with a simple explanation to help you understand how it works.
- Ready-to-Use Queries: Use pre-made queries right away, saving time on creating them yourself.
- Faster Analysis: Quickly analyze data with these ready-to-use queries, getting insights faster.
Now, let’s proceed with the first KQL query and with the explanation.


### 1. Query to detect downtime for each Data Type in Microsoft Sentinel

```kql
// Define an array containing names of security vendorslet array = pack_array("SonicWall","CrowdStrike","Palo Alto Networks","Barracuda");// Combine data from multiple sources into one datasetunion isfuzzy =true(// Include various data sources like logon events, audit logs, etc.union IdentityLogonEvents, IdentityQueryEvents,IdentityDirectoryEvents, SigninLogs, AuditLogs,AADNonInteractiveUserSignInLogs, AADServicePrincipalSignInLogs, AADManagedIdentitySignInLogs, AADUserRiskEvents, AADRiskyUsers, AWSCloudWatch, OfficeActivity, AWSCloudTrail, AzureActivity, PaloAltoPrismaCloudAlert_CL, PaloAltoPrismaCloudAudit_CL,CrowdStrike_Additional_Events_CL, WindowsEvent, SecurityEvent),// Include CommonSecurityLog data and filter it by device vendor(CommonSecurityLog|where DeviceVendor in (array)|extend Type = DeviceVendor),// Include CiscoISEEvent data and label it as "CiscoISE"(CiscoISEEvent|extend Type = "CiscoISE"),// Include CiscoMeraki data and label it as "CiscoMeraki"(CiscoMeraki|extend Type = "CiscoMeraki"),// Include Infoblox data and label it as "InfobloxNIOS"(Infoblox|extend Type = "InfobloxNIOS"),// Include CitrixADCEvent data and label it as "Netscaler"(CitrixADCEvent|extend Type = "Netscaler")// Summarize data by finding the maximum time each vendor was last seen|summarize LastSeen = max (TimeGenerated) by Type// Calculate the time difference between the current time and the last seen time for each vendor|extend TimeDifference = now() - LastSeen// Exclude vendors not seen in the last hour|where TimeDifference >1h
```

### 2. Okta Impossible travel authentication detection

```kql
// Filter data from the Okta table where eventType_s contains "user.authentication.sso"Okta| where eventType_s contains "user.authentication.sso"// Extract geographical information from the request_ipChain_s field and extend them as new columns| extend country_ = tostring(parse_json(tostring(parse_json(request_ipChain_s)[0].geographicalContext)).country)| extend state_ = tostring(parse_json(tostring(parse_json(request_ipChain_s)[0].geographicalContext)).state)| extend city_ = tostring(parse_json(tostring(parse_json(request_ipChain_s)[0].geographicalContext)).city)// Summarize the data by actor_displayName_s (user display name)| summarize city_count = dcount(city_),             city = make_set(city_),             state = make_set(state_),             state_count = dcount(state_),             country = make_set(country_),             User_Email = make_set(actor_alternateId_s) by actor_displayName_s// Filter out rows where the state information is empty| where isnotempty(state) // Filter out rows where the count of unique states/country is greater than 2| where state_count > 2
```

### 3. Netskope query to detect data upload of more than 1 GB

```kql
// Filter data from the Netskope table where server_bytes_d (server bytes transferred) is greater than 1GBNetskope | where server_bytes > 1073741824 // 1GB = 1,073,741,824 bytes// Project selected columns for analysis|project TimeGenerated,protocol_port_s,publisher_name_s,server_bytes_d,src_location_s,src_country_s,sourceport=srcport_d,app_s,appcategory_s,bypass_reason_s,Category,domain_s,dst_country_s,dst_location_s,dst_region_s,dst_zipcode_s,DestinationIP=dstip_s,dstport_d,page_s,site_s,ur_normalized_s,url_s,user_s,userip_s,browser_s,device_s,hostname_s
```

### 4. Signinlog Impossible travel by speed

```kql
// Define the maximum speed thresholdlet maxSpeed = 1000;// Filter data from the SigninLogs table where ResultType is "0" (successful sign-ins)SigninLogs| where ResultType == "0"// Extract latitude from LocationDetails.geoCoordinates and assign it to a new column| extend latitude_ = todouble(parse_json(tostring(LocationDetails.geoCoordinates)).latitude)// Extract longitude from LocationDetails.geoCoordinates and assign it to a new column| extend longitude_ = todouble(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)// Extract country or region from LocationDetails and assign it to a new column| extend countryOrRegion = tostring(LocationDetails.countryOrRegion)// Extract state from LocationDetails and assign it to a new column| extend state = tostring(LocationDetails.state)// Combine state and countryOrRegion into a location string and assign it to a new column| extend location = strcat(state,' - ', countryOrRegion)// Filter out rows where location is empty or undefined| where location <> ' - '// Extract browser information from DeviceDetail and assign it to a new column| extend browser = tostring(DeviceDetail.browser)// Summarize data by UserDisplayName, latitude, longitude, location, browser, AppDisplayName, UserPrincipalName, Location, and state| summarize Count=count(), IP=any(IPAddress), Last=max(TimeGenerated) by UserDisplayName, latitude_, longitude_, Locations=tostring(location), browser, AppDisplayName, UserPrincipalName, Location, state// Pack latitude and longitude into an array and assign it to a new column| extend coordinates = pack_array(latitude_, longitude_)// Summarize data by UserDisplayName, UserPrincipalName, and other attributes, keeping the sets of coordinates, states, countries, timestamps, IPs, apps, and browsers| summarize Coordinates=makeset(coordinates), NumberOfCountries=dcount(Location), NumberOfState=dcount(state), StateCountries=make_set(Locations), Timestamps=makeset(Last), IPs=makeset(IP), Apps=makeset(AppDisplayName), Browsers=makeset(browser) by UserDisplayName, UserPrincipalName// Filter out rows where the number of countries is less than 2 or the number of states is less than 3| where NumberOfCountries > 1  or NumberOfState >2// Calculate the distance between coordinates and assign it to a new column| extend distance = round(geo_distance_2points(todouble(Coordinates[1]),todouble(Coordinates[0]),todouble(Coordinates[3]),todouble(Coordinates[2]))/1000,0)// Calculate the time difference in hours between timestamps and assign it to a new column| extend hours = abs(datetime_diff('hour', todatetime(Timestamps[1]),todatetime(Timestamps[0])))// Filter out rows where hours is equal to 0| where hours > 0// Calculate the speed in kilometers per hour and assign it to a new column| extend speedKmPerHour = round(distance/hours,0)// Filter out rows where speed is greater than the maximum speed threshold| where speedKmPerHour > maxSpeed// Reorder and project selected columns, excluding IPs, and sort by UserPrincipalName in ascending order| project-reorder Timestamps, UserDisplayName, UserPrincipalName, IPs, speedKmPerHour, distance, hours, StateCountries, Apps, Browsers, StateCountries| sort by UserPrincipalName asc// Replace special characters in IPs and assign it to a new column| extend IPs = replace('[\\[|\\|\\\\|"\\]]','',tostring(IPs))
```

### 5. TI correlation of malicious IP with web session and filtering allowed IP from Palo alto with Whitelisting.

```kql
// Define an array of IP addresses considered as whitelist rangeslet IP_whitelist_ranges = dynamic(["1.0.0.0/24", "2.3.5.48", ...]); // IP address whitelist ranges// Maximum number of IOCs to considerlet HAS_ANY_MAX = 10000;// Lookback time for web session logslet dt_lookBack = 1h;// Lookback time for threat intelligence indicatorslet ioc_lookBack = 14d;// Retrieve threat intelligence indicators and filter relevant informationlet IP_TI = ThreatIntelligenceIndicator  | where TimeGenerated >= ago(ioc_lookBack)  | summarize LatestIndicatorTime = arg_max(TimeGenerated, *) by IndicatorId  | where Active == true and ExpirationDateTime > now()  | extend TI_ipEntity = coalesce(NetworkIP, NetworkDestinationIP, NetworkSourceIP, EmailSourceIpAddress, "NO_IP")  | where TI_ipEntity != "NO_IP"  | where ipv4_is_private(TI_ipEntity) == false and TI_ipEntity !startswith "fe80" and TI_ipEntity !startswith "::" and TI_ipEntity !startswith "127.";// Retrieve source IP addresses from CommonSecurityLog within the last hourlet src_ip = CommonSecurityLog| where TimeGenerated >= ago(1h)| where DeviceProduct contains "PAN-OS" and DeviceAction == 'allow'| where isnotempty(SourceIP) and isnotempty(DestinationIP)| project SourceIP, DestinationIP;// Retrieve destination IP addresses from CommonSecurityLog within the last hourlet dst_ip = CommonSecurityLog| where TimeGenerated >= ago(1h)| where DeviceProduct contains "PAN-OS" and DeviceAction == 'allow'| where isnotempty(SourceIP) and isnotempty(DestinationIP)| project SourceIP, DestinationIP;// Join threat intelligence indicators with source IP addresseslet IP_TI_Src = IP_TI| join kind=innerunique (src_ip) on $left.TI_ipEntity == $right.SourceIP;// Join threat intelligence indicators with destination IP addresseslet IP_TI_Dst= IP_TI| join kind=innerunique (dst_ip) on $left.TI_ipEntity == $right.DestinationIP;// Combine threat intelligence indicators for source and destination IP addresseslet IP_TI_CS = union IP_TI_Dst, IP_TI_Src;// Calculate the list of threat intelligence IPslet IP_TI_list = toscalar(IP_TI_CS| summarize NIoCs = dcount(TI_ipEntity), IoCs = make_set(TI_ipEntity)| project IoCs = iff(NIoCs > HAS_ANY_MAX, dynamic([]), IoCs));// Join threat intelligence indicators with web session logs and filter non-whitelisted IPsIP_TI_CS| join kind=innerunique (    _Im_WebSession (starttime = ago(dt_lookBack), srcipaddr_has_any_prefix = IP_TI_list)    | where isnotempty(SrcIpAddr)    | extend imNWS_TimeGenerated = TimeGenerated)on $left.TI_ipEntity == $right.SrcIpAddr| where imNWS_TimeGenerated < ExpirationDateTime| summarize imNWS_TimeGenerated = arg_max(imNWS_TimeGenerated, *) by IndicatorId, DstIpAddr| project imNWS_TimeGenerated , Description, ActivityGroupNames, IndicatorId, ThreatType, ExpirationDateTime, ConfidenceScore,  TI_ipEntity, Dvc, SrcIpAddr, DstIpAddr, Url, Type,SourceIP,DestinationIP, Action, DvcAction| extend srcip = ipv4_is_in_any_range(SrcIpAddr,IP_whitelist_ranges)| extend dstip = ipv4_is_in_any_range(DstIpAddr,IP_whitelist_ranges)| where srcip == false and dstip == false
```

## Conclusion
In conclusion, harnessing the power of customized KQL queries for detection purposes offers a potent solution to extract actionable insights from data. By tailoring queries to specific detection needs, analysts can efficiently identify patterns, anomalies, and trends crucial for informed decision-making. Embracing this approach not only enhances detection capabilities but also exemplifies a proactive stance towards data analysis, ultimately driving better outcomes for organizations.


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
