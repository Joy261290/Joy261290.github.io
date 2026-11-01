---
title: "Lab Challenge Writeup: Hack The Box - Windows Fundamentals"
date: 2026-09-15 10:00:00 +0300
categories: [Lab Challenges, CTFs]
tags: [windows, hackthebox, security-auditing, powershell, wmi, ntfs]
---

## 1. Introduction & Module Overview
This module delves deep into Windows Operating System architecture, file systems, user security management, and administrative services[cite: 3]. Windows environments represent the primary attack surface in most enterprise networks, making this foundational knowledge critical for security analysts[cite: 3]. The objective was to gain hands-on experience navigating Windows via the GUI, PowerShell, and remote management tools to analyze system configurations and permissions[cite: 3].

---

## 2. Operating System Structure & Remote Access

### Accessing the Target Workstation
* **OpenVPN Connection:** Established a secure connection to the Hack The Box internal laboratory network on Kali Linux[cite: 3, 4]:
  ```bash
  sudo openvpn academy-regular.ovpn

  ---

## 3. File System & NTFS Permissions

### Auditing Access Controls (`icacls`)
Explored NTFS capabilities such as logging and permission inheritance[cite: 8]. 
* **Question:** What system user has full control over the `c:\users` directory?[cite: 8]
* **Answer:** `bob.smith`[cite: 8]
* **Methodology:** Executed `icacls c:\users` in PowerShell[cite: 8]. The output listed `WS01\bob.smith:(OI)(CI)(F)`, indicating explicit Full Control with object and container inheritance[cite: 8].

---

## 4. NTFS vs. Share Permissions & SMB

### Network Sharing & Logging
* **Protocols & Utilities:** Examined the Server Message Block (**SMB**) protocol used for sharing files and printers, and utilized **Event Viewer** to investigate share access logs[cite: 9, 10].
* **Company Data Share Path:** `C:\Users\htb-student\Desktop\Company Data`[cite: 10]  
  * *Methodology:* Verified active network shares using the `net share` command in PowerShell[cite: 11].

---

## 5. Windows Registry

### Inspecting and Modifying Hives
The Windows Registry functions as a hierarchical database storing critical OS configurations structured into hives, keys, and values[cite: 11].
* **Disabling UAC (`EnableLUA`):** Navigated to `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System` and modified `EnableLUA` from `1` to `0`[cite: 11, 12].
  * *Result:* Querying via `reg query` returned a hexadecimal value of `0x0`[cite: 11].
* **Registry Hives & Data Types:**
  * Currently logged-on user settings hive: `HKEY_CURRENT_USER` (HKCU)[cite: 12, 13]
  * `EnableLUA` data type: `REG_DWORD`[cite: 13]
  * Multiple text strings storage data type: `REG_MULTI_SZ`[cite: 13, 14]

---

## 6. Windows Services & Processes

### Identifying Background Operations
* **Non-Standard Update Service:** Identified `Foxit ReaderUpdateService.exe` running on the host[cite: 14, 15].
  * *Methodology:* Opened Task Manager, filtered services by description containing "Update", and cross-referenced PIDs under the **Details** tab to extract the underlying executable binary name[cite: 14, 15, 30].

---

## 7. PowerShell & WMI Management

### Core Cmdlets & Execution Policies
* **IPConfig Alias:** `ifconfig` (Discovered via `get-alias`)[cite: 16]
* **LocalMachine Execution Policy:** `Unrestricted` (Discovered via `Get-ExecutionPolicy -List`)[cite: 17]
* **System Serial Number:** `00329-10280-00000-AA938`[cite: 18]  
  * *Methodology:* `Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory, BuildNumber, SerialNumber, Version | ft`[cite: 18].

---

## 8. Windows Security & Identifiers (SIDs)

### User Security & Startup Applications
* **bob.smith SID:** `S-1-5-21-2614195641-1726409526-3792725429-1003`[cite: 19]  
  * *Methodology:* Queried WMI account objects using `Get-WmiObject -Class Win32_UserAccount -filter "name = 'bob.smith'"`[cite: 19, 20].
* **Disabled 3rd-Party Security App:** `NordVPN` (Identified via Task Manager's Startup tab)[cite: 20, 21].

---

## 9. Skills Assessment Summary

In the final practical module assessment, custom administrative objects and permissions were configured:
* **Default Share Permissions Group:** `Everyone`[cite: 21, 26]
* **NTFS Permissions Tab Name:** `Security`[cite: 27]
* **Windows Update Service Name:** `wuauserv`[cite: 27, 28]
* **Created User (`Jim`) SID:** `S-1-5-21-2614195641-1726409526-3792725429-1006`[cite: 28]
* **Created Security Group (`HR`) SID:** `S-1-5-21-2614195641-1726409526-3792725429-1007`[cite: 29]

---

## 10. Challenges & How They Were Resolved

1. **RDP Connection Errors:** Initial execution of `xfreerdp` threw unparsed literal placeholder errors (`targetIp`) and a missing binary error (`xfreerdp` not found)[cite: 4, 30]. Resolved by switching to `xfreerdp3` and supplying the explicit target IP address[cite: 4, 30].
2. **Overwhelming Service Lists:** Task Manager presented an overwhelming volume of background processes when hunting for update services[cite: 14, 30]. Resolved by filtering descriptions for "Update" and verifying the executable binary name inside the **Details** tab[cite: 14, 30].
3. **Targeted WMI Filtering:** Generic `Get-WmiObject` queries returned overall OS data instead of specific user attributes[cite: 19, 30]. Resolved by implementing precise `-filter` parameters (`Get-WmiObject -Class Win32_UserAccount -filter ...`) and querying local accounts via `Get-LocalUser`[cite: 19, 30].

---

## 11. Conclusion & Key Takeaways
Completing the Windows Fundamentals module strengthened practical abilities to audit OS configurations, evaluate NTFS access control lists (`icacls`), and inspect system services via PowerShell and WMI[cite: 3, 30]. Misconfigured local NTFS and share permissions can easily expose sensitive enterprise directories to non-administrative users[cite: 30]. Prioritizing command-line auditing tools over GUI-based workflows enables security analysts to inspect access controls faster and more thoroughly during host investigations[cite: 30].
