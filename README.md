# ✅ STIG Remediation: Windows Application Log Size (WN10-AU-000500)

## 🛡️ What is a DISA STIG?

A **DISA STIG (Security Technical Implementation Guide)** is a configuration standard developed by the 
Defense Information Systems Agency (DISA) to enhance the security of information systems within the Department 
of Defense (DoD). STIGs provide specific guidelines and technical controls that systems must follow to reduce 
vulnerabilities and ensure secure configurations.

These benchmarks serve a critical role in compliance, helping organizations ensure their systems meet rigorous 
security requirements defined by the DoD. While originally intended for federal use, they are widely adopted in 
private sector environments for strengthening cybersecurity postures.

## 📘 Overview

This lab demonstrates how to identify, remediate, and validate a STIG compliance issue using **PowerShell** and **Tenable.io** vulnerability scans.  
We target **STIG ID: WN10-AU-000500**, which requires the **Application Event Log size** to be configured to **at least 32,768 KB (32MB)**.

---

## 🧪 Environment

| Component         | Details                        |
|------------------|--------------------------------|
| OS               | Windows 10                     |
| STIG ID          | WN10-AU-000500                 |
| Tool Used        | Tenable.io                     |
| Scan Type        | Advanced Network Scan          |
| Scanner Engine   | LOCAL-SCAN-ENGINE-01           |
| Remediation      | Manual & PowerShell            |

---

## 🔧 Step-by-Step Remediation Process

### 🔴 Step 1: Run Initial Tenable Scan

The initial scan identified a failed control for STIG ID `WN10-AU-000500`.

📸 **Screenshot – Initial Failed Scan (Scan 1):**  

<img width="1250" alt="Screen Shot 2025-05-06 at 1 07 19 PM" src="https://github.com/user-attachments/assets/adf4275d-5799-42d0-9a7c-55cd15829619" />

---

### 🔴 Step 2: Confirm Issue Persists with Second Scan

A second scan was run to confirm consistency of the finding before remediation.

📸 **Screenshot – Confirmed Failed STIG (Scan 2):**  

<img width="1250" alt="Screen Shot 2025-05-06 at 3 56 33 PM" src="https://github.com/user-attachments/assets/1ed0214d-4829-4bd6-b374-11f85e7f1f73" />

---

### 🧾 Step 3: Manually Remediate via Registry Editor

The registry key `MaxSize` was manually created and set to `32768`.

📸 **Screenshot – Registry Editor (Manual Fix):**  

![Screen Shot 2025-05-06 at 1 01 44 PM](https://github.com/user-attachments/assets/2868f8df-f68d-4bf8-8e58-0c01352431c3)

---

### ✅ Step 4: Run Third Scan to Validate Manual Fix

Scan results show that the manual registry change successfully remediated the issue.

📸 **Screenshot – Passed After Manual Fix (Scan 3):**  

<img width="1250" alt="Screen Shot 2025-05-06 at 2 13 19 PM" src="https://github.com/user-attachments/assets/90977858-48b5-4830-bec6-d919286c0c5f" />

---

### 💻 Step 5: Revert Fix, Reapply Using PowerShell Script

The registry value was removed, and then reapplied using the PowerShell script below.

```powershell
Write-Host "`n[+] Last CMD Command:" -ForegroundColor Cyan
cmd /c "doskey /history" | Select-Object -Last 1

$regPath = "HKLM:\\SOFTWARE\\Policies\\Microsoft\\Windows\\EventLog\\Application"
$propertyName = "MaxSize"
$propertyValue = 0x8000  # 32,768 KB

if (-not (Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

New-ItemProperty -Path $regPath -Name $propertyName -PropertyType DWord -Value $propertyValue -Force | Out-Null
Write-Host "`n[✔] Registry setting applied successfully!" -ForegroundColor Green
```

### 🔄 Step 6: Verify Registry Value via GUI

![Screen Shot 2025-05-06 at 1 01 44 PM](https://github.com/user-attachments/assets/da6424b8-e2a1-4fd7-9cb3-d638555ec3ac)

---

### ✅ Step 7: Final Tenable Scan – PowerShell Fix Validated

📸 Screenshot – Passed After Script Fix (Scan 4):

<img width="1717" alt="Screen Shot 2025-05-06 at 5 28 56 PM" src="https://github.com/user-attachments/assets/61507e62-f0f8-459f-b867-5d947309305e" />

### 🧠 Reference Page from STIG Viewer

📸 STIG Remediation PowerShell Script & Explanation:

```
<#
.SYNOPSIS
    This PowerShell script ensures that the maximum size of the Windows Application event log is at least 32768 KB (32 MB).

.NOTES
    Author          : James Moore
    LinkedIn        : linkedin.com/in/jamesmoore1983
    GitHub          : github.com/techgneek
    Date Created    : 2025-06-05
    Last Modified   : 2025-06-05
    Version         : 1.0
    CVEs            : N/A
    Plugin IDs      : N/A
    STIG-ID         : WN10-AU-000500

.TESTED ON
    Date(s) Tested  : 2025-06-05
    Tested By       : James Moore
    Systems Tested  : 
    PowerShell Ver. : 

.USAGE
    Put any usage instructions here.
    Example syntax:
    PS C:\> .\__remediation_template(STIG-ID-WN10-AU-000500).ps1 
#>


# Filename: Update-EventLogPolicy.ps1

# --- Part 1: Get last CMD command used ---
Write-Host "`n[+] Last CMD Command:" -ForegroundColor Cyan
cmd /c "doskey /history" | Select-Object -Last 1

# --- Part 2: Set EventLog MaxSize Policy ---
$regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application"
$propertyName = "MaxSize"
$propertyValue = 0x8000  # 32,768 KB

# Create the key if it doesn't exist
if (-not (Test-Path $regPath)) {
    Write-Host "[+] Registry path not found. Creating path..." -ForegroundColor Yellow
    New-Item -Path $regPath -Force | Out-Null
}

# Set the MaxSize DWORD value
Write-Host "[+] Setting MaxSize to $propertyValue (32MB) at $regPath" -ForegroundColor Cyan
New-ItemProperty -Path $regPath -Name $propertyName -PropertyType DWord -Value $propertyValue -Force | Out-Null

Write-Host "`n[✔] Registry setting applied successfully!" -ForegroundColor Green
```
---

### 📌 Summary of Remediation Flow

- Initial scan identified STIG failure.
- Second scan confirmed repeatability.
- Manually set registry value to 32768.
- Scan verified manual remediation worked.
- Reset and applied fix via PowerShell script.
- Final scan confirmed PowerShell-based fix passed.

---






