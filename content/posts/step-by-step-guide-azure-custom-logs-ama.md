---
title: "Step-by-Step Guide to Azure Custom Logs via AMA"
date: "2025-03-09"
author: "Sujit Mahakhud"
tags: ["AI", "Azure", "Azure Sentinel", "Cloud", "Cloud Security", "Microsoft", "Microsoft Sentinel", "Microsoft Sentinel Implementation", "Security"]
categories: ["Azure Sentinel", "Cyber Security", "Microsoft Security", "Microsoft Sentinel"]
original_url: "https://secbyte.in/2025/03/09/step-by-step-guide-to-azure-custom-logs-via-ama/"
---

# Step-by-Step Guide to Azure Custom Logs via AMA

## Introduction

Many applications generate logs in text files and store them in different file paths rather than using standard logging services like Windows Event Log or Syslog. This can create challenges in security monitoring and analysis, as these logs are not directly accessible in Microsoft Sentinel.

The Azure Monitor Agent (AMA) addresses this issue by enabling log collection from text files—regardless of their format—on both Windows and Linux machines. Additionally, AMA allows real-time data transformation during ingestion, making log parsing and analysis more efficient.

With AMA, you can:

✅ Collect application logs stored in **custom text files**.

✅ **Transform and parse logs** during ingestion into different fields.

✅ **Ingest logs from multiple paths** across different systems into **Microsoft Sentinel**.

This guide walks you through a **step-by-step** process to configure **custom log ingestion** from text files stored in different paths using the **Custom Logs via AMA data connector**.

## 🔍 Prerequisites

Before configuring the data ingestion, ensure that:

- The **Azure Monitor Agent (AMA)** is installed on the **VM(s)** from which logs will be collected.
- You have **appropriate permissions** (such as **Security Administrator** or **Log Analytics Contributor**) to configure **Data Connectors** and **Data Collection Rules (DCRs)**.

## 🚀 Step-by-Step Guide to Ingest Custom Logs via AMA

### 1️⃣ Access Microsoft Sentinel Data Connectors

#### In the Azure Portal:

1. Navigate to **Microsoft Sentinel** in the **Azure portal**.
2. Under **Configuration**, select **Data connectors**.

### 2️⃣ Select the Custom Logs via AMA Connector

1. In the **Search** box, type `custom`.
2. From the results, select **Custom Logs via AMA**.
3. Click **Open connector page**.

### 3️⃣ Create a Data Collection Rule (DCR)

1. In the **Configuration** area, select **+Create data collection rule**.

2. In the **Basic** tab, enter the following details:
   - 🏷 **DCR Name**: Provide a **descriptive name** for the **Data Collection Rule**.
   - 🔹 **Subscription**: Select your **Azure subscription**.
   - 📁 **Resource Group**: Choose the **resource group** where you want to store this **DCR**.

3. Click **Next: Resources >**.

### 4️⃣ Define VM Resources

1. In the **Resources** tab, select the **Virtual Machines** from which logs need to be collected.
   - These could be **application servers** or **log forwarders**.
2. Review your selections and click **Next: Collect >**.

### 5️⃣ Configure Data Collection for Your Application

1. In the **Collect** tab:
   - From **Select device type (optional)**, choose your **application or device type**.
   - If your application is not listed, select **Custom new table**.

2. Configure table settings:
   - **Table Name**: If using a **custom table**, enter a name ending with `_CL`.
   - **File Pattern**: Provide the **full path and filename** of the logs to be collected.
     - Example: `/var/log/app_logs/*.log`
     - Wildcards can be used in filenames (e.g., `*.log`).

3. In the **Transform** field:
   - If using a **custom table**, enter a **Kusto Query Language (KQL)** transformation.
   - If using a **predefined device type**, the transformation is **pre-configured** and should not be modified.
   - Possible values:
     - `source` (default, no transformation)
     - `source | project-rename Message=RawData` (for logs forwarded via a log forwarder)

4. Click **Next: Review + Create**.

### 6️⃣ Review and Create the Data Collection Rule

1. Review all configurations.
2. Click **Create** to finalize the **Data Collection Rule**.

## ✅ Verifying Data Ingestion in Microsoft Sentinel

After a few minutes, verify that the logs are being ingested:

1. Go to **Microsoft Sentinel** > **Logs**.
2. Run a query to check if data is arriving: `CustomTable_CL | limit 10`
3. If no logs appear, verify:
   - The **log files exist** in the defined path.
   - The **AMA agent** is running on the **VM**.
   - The correct **file path and table name** were configured.

## 🎯 Conclusion

The **Custom Logs via AMA** data connector provides a flexible way to ingest **application logs and custom text logs** into **Microsoft Sentinel**. By following these steps, security teams can efficiently integrate **specific log sources**, enabling **enhanced threat detection and security monitoring**.

## Check Out My YouTube Channel

📺 Watch the detailed video walkthrough:
[Microsoft Sentinel Training Day 21: Step-by-Step Linux VM Integration with Sentinel using DCR](https://www.youtube.com/watch?v=4QEkm3og6Wk)

### 📌 Next Steps

✅ Explore **KQL queries** to analyze the ingested logs.

✅ Set up **alert rules** based on the collected log data.

✅ Automate responses using **Microsoft Sentinel Playbooks**.

💡 Have any questions or need further guidance? Let me know in the comments below! 🚀

---

**Want to learn more about Microsoft Sentinel?**

- Connect on LinkedIn: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- Subscribe on YouTube: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- Email me: [sujitmahakhud@gmail.com](mailto:sujitmahakhud@gmail.com)

🧩 Read this article on the official website: [secbyte.in](https://secbyte.in/2025/03/09/step-by-step-guide-to-azure-custom-logs-via-ama/)
