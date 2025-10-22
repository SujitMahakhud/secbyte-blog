---
title: "Automate Data Collection Rule and Virtual Machine Association Using PowerShell in Microsoft Sentinel"
date: 2024-12-06
author: "Sujit Mahakhud"
categories:
  - Microsoft Sentinel
  - Automation
  - PowerShell
tags:
  - Azure Monitor
  - Data Collection Rules
  - DCR
  - Azure VM
  - Arc Server
  - Automation
  - KQL
original_url: "https://secbyte.in/2025/09/26/automating-azure-vm-arc-server-data-collection-rule-association-with-powershell/"
description: "Learn how to automate Azure VM and Arc Server onboarding to Data Collection Rules (DCRs) using PowerShell. This script simplifies agent installation and DCR association at scale."
---

# Automate Data Collection Rule and Virtual Machine Association Using PowerShell in Microsoft Sentinel

## Overview

In Microsoft Sentinel or Azure Monitor deployments, onboarding virtual machines and Arc-enabled servers to **Data Collection Rules (DCRs)** is a key step. Doing this manually for dozens (or hundreds) of machines is time-consuming and error-prone.

This PowerShell script automatically:
- Detects whether a machine is an **Azure VM** or an **Arc-enabled server**
- Installs the **Azure Monitor Agent (AMA)** if it's missing
- Associates each machine with the correct **Data Collection Rule (DCR)**
- Logs the success or failure for each machine to a CSV file for auditing

This script works for **Windows and Linux machines** and supports **multiple subscriptions**.

---

## Why This Script Matters

- **Time saving**: Automates manual association of machines to DCRs
- **Error reduction**: Automatically detects machine type and OS
- **Scalable**: Processes an entire list of machines from a CSV file in one go
- **Auditable**: Exports a detailed report of results

---

## How the Script Works

1. **Validate modules & Azure session**: Ensures required Az PowerShell modules are installed and you're logged in
2. **Read machine details from CSV**: Subscription IDs, Resource Groups, DCR name, machine type, etc.
3. **Detect VM or Arc server**: Uses `Get-AzVM` and `Get-AzConnectedMachine`
4. **Install AMA if missing**: For VMs, uses `Set-AzVMExtension`; for Arc machines, uses `New-AzConnectedMachineExtension`
5. **Associate with DCR**: Runs `New-AzDataCollectionRuleAssociation`
6. **Log results**: Summarizes successes and failures to the console and an output CSV

---

## Prerequisites

- **Azure PowerShell modules**: Az.Accounts, Az.Compute, Az.ConnectedMachine, Az.Monitor, Az.Resources
- **Azure permissions**: Contributor role on resource groups for VMs and DCRs
- **CSV file** with machine details (see format below)

---

## CSV File Format

The script expects a CSV file with the following columns:

| MachineName | VmSubscriptionID | DcrSubscriptionID | VmResourceGroup | DcrResourceGroup | DcrName | MachineType |
|-------------|-----------------|-------------------|-----------------|------------------|---------|-------------|
| MyVM01 | 111-aaa-bbb-ccc-111 | 222-aaa-bbb-ccc-222 | RG-VM | RG-DCR | My-DCR-Rule | VM |
| MyArcServer | 111-aaa-bbb-ccc-111 | 222-aaa-bbb-ccc-222 | RG-Arc | RG-DCR | My-DCR-Rule | Arc |

**Column Descriptions:**
- **MachineName**: Exact VM or Arc machine name
- **VmSubscriptionID**: Subscription ID where the machine resides
- **DcrSubscriptionID**: Subscription ID where the DCR resides
- **VmResourceGroup**: Resource group of the VM or Arc server
- **DcrResourceGroup**: Resource group of the DCR
- **DcrName**: Data Collection Rule name
- **MachineType**: "VM" or "Arc" (optional — script auto-detects if blank)

Save this file as `machines.csv`.

---

## Running the Script

### Option 1: Azure Cloud Shell

1. Open the Azure Portal
2. Click the **Cloud Shell** icon in the top navigation bar
3. Choose **PowerShell** as your shell type
4. Upload both files (script and CSV)
5. If the file uploaded as `.txt`, rename it:
   ```powershell
   mv dcrassociation.txt dcrassociation.ps1
   ```
6. Run the script:
   ```powershell
   Connect-AzAccount  # if not already logged in
   ./dcrassociation.ps1
   ```

### Option 2: Local PowerShell

1. Install Az modules if not already installed:
   ```powershell
   Install-Module Az -Force
   ```
2. Save the PowerShell script and `machines.csv` locally
3. Open PowerShell as Administrator
4. Authenticate:
   ```powershell
   Connect-AzAccount
   ```
5. Run the script:
   ```powershell
   .\dcrassociation.ps1
   ```

---

## Output & Logs

At the end, the script prints a summary such as:

```
Summary: Total=10, Success=9, Failed=1
Failures:
- MyArcServer02: Machine not found
Results exported to C:\Users\...\DCR_Assoc_Results_20250926.csv
Script completed.
```

You can open the exported CSV to see details of each machine's association.

---

## Tips for Best Results

- Ensure the **account** you use has the right permissions on both the VM/Arc machine and the DCR subscription
- Make sure **Azure Monitor Agent** can be installed (Windows/Linux firewall and policy permissions)
- Double-check the **machine names** in your CSV match Azure exactly

---

## Related Resources

- [Microsoft Sentinel Documentation](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor Agent Overview](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview)
- [Data Collection Rules](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview)

---

## Tags
`#MicrosoftSentinel` `#Azure` `#PowerShell` `#Automation` `#DataCollectionRules` `#AzureMonitor` `#DCR` `#AMA`

---

**Author:** Sujit Mahakhud  
**Website:** [www.secbyte.in](https://www.secbyte.in)  
**Published:** December 6, 2024
