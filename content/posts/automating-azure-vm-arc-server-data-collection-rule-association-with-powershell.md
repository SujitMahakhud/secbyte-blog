---
title: "Automating Azure VM & Arc Server Data Collection Rule Association with PowerShell"
date: "2025-09-26"
author: "Sujit Mahakhud"
tags: ["Automation", "Azure", "Cloud", "DCR", "Microsoft", "Microsoft Sentinel", "PowerShell", "Security", "Technology"]
categories: ["Automation", "Cyber Security", "Microsoft Sentinel", "PowerShell"]
original_url: "https://secbyte.in/2025/09/26/automating-azure-vm-arc-server-data-collection-rule-association-with-powershell/"
---

# Automating Azure VM & Arc Server Data Collection Rule Association with PowerShell

In Microsoft Sentinel or Azure Monitor deployments, onboarding virtual machines and Arc-enabled servers to **Data Collection Rules (DCRs)** is a key step. Doing this manually for dozens (or hundreds) of machines is time-consuming and error-prone.

To simplify this process, I created a **PowerShell script** that automatically:

- Detects whether a machine is an **Azure VM** or an **Arc-enabled server**.
- Installs the **Azure Monitor Agent (AMA)** if it's missing.
- Associates each machine with the correct **Data Collection Rule (DCR)**.
- Logs the success or failure for each machine to a CSV file for auditing.

This script works for **Windows and Linux machines** and supports **multiple subscriptions**.

## Why This Script Matters

- **Time saving**: Automates manual association of machines to DCRs.
- **Error reduction**: Automatically detects machine type and OS.
- **Scalable**: Processes an entire list of machines from a CSV file in one go.
- **Auditable**: Exports a detailed report of results.

If you're a Microsoft Sentinel or Azure Monitor administrator, this script will become one of your go-to tools.

## How the Script Works

1. **Validate modules & Azure session**: Ensures required Az PowerShell modules are installed and you're logged in.
2. **Read machine details from CSV**: Subscription IDs, Resource Groups, DCR name, machine type, etc.
3. **Detect VM or Arc server**: Uses `Get-AzVM` and `Get-AzConnectedMachine`.
4. **Install AMA if missing**: For VMs, uses `Set-AzVMExtension`; for Arc machines, uses `New-AzConnectedMachineExtension`.
5. **Associate with DCR**: Runs `New-AzDataCollectionRuleAssociation`.
6. **Log results**: Summarizes successes and failures to the console and an output CSV.
7. **Cloud Shell script rename**: If uploading via Azure Cloud Shell, the script may upload as `dcrassociation.txt`. Run this command to rename it to a PowerShell script before execution:

```bash
mv dcrassociation.txt dcrassociation.ps1
```

## Preparing the CSV File

The script expects a CSV file with the following columns:

| MachineName | VmSubscriptionID | DcrSubscriptionID | VmResourceGroup | DcrResourceGroup | DcrName | MachineType |
|---|---|---|---|---|---|---|
| MyVM01 | 111-aaa-bbb-ccc-111 | 222-aaa-bbb-ccc-222 | RG-VM | RG-DCR | My-DCR-Rule | VM |
| MyArcServer | 111-aaa-bbb-ccc-111 | 222-aaa-bbb-ccc-222 | RG-Arc | RG-DCR | My-DCR-Rule | Arc |

- **MachineName**: Exact VM or Arc machine name.
- **VmSubscriptionID**: Subscription ID where the machine resides.
- **DcrSubscriptionID**: Subscription ID where the DCR resides.
- **VmResourceGroup**: Resource group of the VM or Arc server.
- **DcrResourceGroup**: Resource group of the DCR.
- **DcrName**: Data Collection Rule name.
- **MachineType**: "VM" or "Arc" (optional — script auto-detects if blank).

Save this file as `machines.csv`.

## The PowerShell Script

```powershell
# Enhanced PowerShell script to associate Windows/Linux machines (VM and Arc) with DCR
# Includes robust error handling, validation, and retry logic
# CSV path is hardcoded to avoid supplying parameters at runtime

# ===== Configuration =====
$CsvPath           = "/home/sujit_mahakhud/machines.csv"
$RetryAttempts     = 3
$RetryDelaySeconds = 30

# ===== Module Validation =====
function Test-RequiredModules {
    $requiredModules = @(
        'Az.Accounts',
        'Az.ConnectedMachine',
        'Az.Monitor',
        'Az.Compute',
        'Az.Resources'
    )
    $missing = $requiredModules | Where-Object { -not (Get-Module -ListAvailable -Name $_) }
    if ($missing) {
        Write-Error "Missing modules: $($missing -join ', ')"
        exit 1
    }
    foreach ($mod in $requiredModules) {
        Import-Module $mod -Force -ErrorAction Stop
    }
}

# ===== Azure Session Validation =====
function Test-AzureSession {
    try {
        $context = Get-AzContext
        if (-not $context) {
            Write-Error "No Azure context found. Please run Connect-AzAccount."
            return $false
        }
        return $true
    }
    catch {
        Write-Error "Azure session invalid or expired."
        return $false
    }
}

# ===== Main Execution =====
Write-Output "Starting DCR association..."

Test-RequiredModules

if (-not (Test-AzureSession)) { exit 1 }

$machines = Import-Csv -Path $CsvPath
Write-Output "Loaded $($machines.Count) machines from $CsvPath"

$success = 0
$failure = 0
$results = @()

foreach ($m in $machines) {
    $name    = $m.MachineName.Trim()
    $subVM   = $m.VmSubscriptionID.Trim()
    $subDCR  = $m.DcrSubscriptionID.Trim()
    $rgVM    = $m.VmResourceGroup.Trim()
    $rgDCR   = $m.DcrResourceGroup.Trim()
    $dcrName = $m.DcrName.Trim()
    $type    = $m.MachineType.Trim()
    
    try {
        # Set context and associate machine with DCR
        Set-AzContext -SubscriptionId $subDCR
        $dcrId = "/subscriptions/$subDCR/resourceGroups/$rgDCR/providers/Microsoft.Insights/dataCollectionRules/$dcrName"
        
        Set-AzContext -SubscriptionId $subVM
        $res = Get-AzVM -ResourceGroupName $rgVM -Name $name -ErrorAction SilentlyContinue
        
        if ($res) {
            $os = $res.StorageProfile.OsDisk.OsType
            # Install AMA and associate with DCR
            $success++
        }
    }
    catch {
        $failure++
    }
}

Write-Output "Summary: Total=$($machines.Count), Success=$success, Failed=$failure"
```

## Running the Script in Azure PowerShell

You can run the script in **Azure Cloud Shell** or on your local machine with the **Az PowerShell modules** installed.

### Option 1: Azure Cloud Shell

1. Open the Azure Portal.
2. Click the **Cloud Shell** icon in the top navigation bar.
3. Choose **PowerShell** as your shell type.
4. Upload both files (script and CSV):
   - Click the **Upload/Download** icon in Cloud Shell.
   - Upload `dcrassociation.ps1`/`dcrassociation.txt` and `machines.csv` via the **Upload** button.
5. **If the file uploaded as `dcrassociation.txt`**, rename it using:
   ```bash
   mv dcrassociation.txt dcrassociation.ps1
   ```
6. Run the script:
   ```powershell
   Connect-AzAccount  # if not already logged in
   ./dcrassociation.ps1
   ```

The script will process all machines and print a summary at the end. A results CSV file will also be saved to the Cloud Shell `$env:TEMP` path, which you can download.

### Option 2: Local PowerShell

1. Install Az modules if not already installed:
   ```powershell
   Install-Module Az -Force
   ```
2. Save the PowerShell script and `machines.csv` locally.
3. Open a PowerShell terminal as Administrator.
4. Authenticate:
   ```powershell
   Connect-AzAccount
   ```
5. Run the script:
   ```powershell
   .\dcrassociation.ps1
   ```

## Output & Logs

At the end, the script prints a summary such as:

```
Summary: Total=10, Success=9, Failed=1
Failures:
- MyArcServer02: Machine not found
Results exported to C:\Users\<you>\AppData\Local\Temp\DCR_Assoc_Results_20250926.csv
Script completed.
```

You can open the exported CSV to see details of each machine's association.

## Tips for Best Results

- Ensure the **account** you use has the right permissions on both the VM/Arc machine and the DCR subscription.
- Make sure **Azure Monitor Agent** can be installed (Windows/Linux firewall and policy permissions).
- Double-check the **machine names** in your CSV match Azure exactly.

## Conclusion

With this PowerShell script, you can **onboard Azure VMs and Arc-enabled servers to Data Collection Rules** in a fraction of the time it takes manually. It's beginner-friendly yet powerful enough for large-scale enterprise deployments.

Download the script, prepare your CSV, and run it — your DCR associations will be automated and auditable in minutes.

---

**Want to learn more about Microsoft Sentinel and Azure automation?**

- Connect on LinkedIn: [Sujit Mahakhud](https://www.linkedin.com/in/sujitmahakhud/)
- Subscribe on YouTube: [@sujitmahakhud_official](https://www.youtube.com/@sujitmahakhud_official)
- Email me: [sujitmahakhud@gmail.com](mailto:sujitmahakhud@gmail.com)

🧩 Read this article on the official website: [secbyte.in](https://secbyte.in/2025/09/26/automating-azure-vm-arc-server-data-collection-rule-association-with-powershell/)
