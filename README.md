# 🛡️ Project: Wazuh SIEM + Active Directory Detection Lab

## 🎯 Objective
To simulate a real-world corporate network environment using Windows Active Directory and learn to detect/analyze common cyber attacks. This project involved:
1.  Building a virtual network with a Domain Controller (Windows Server) and a Workstation (Windows 11).
2.  Installing and configuring a **Wazuh SIEM** server on the host machine.
3.  Deploying Wazuh agents and Sysmon to monitor the Windows VMs.
4.  Simulating attacker techniques from the **MITRE ATT&CK** framework.
5.  Using the Wazuh dashboard to hunt for, detect, and analyze the malicious activity in real-time.

---

## ✅ Skills Learned
* **SIEM Deployment:** Installed and configured the Wazuh all-in-one server.
* **Endpoint Monitoring:** Deployed Wazuh agents and Sysmon on Windows endpoints.
* **Active Directory Security:** Built a Domain Controller (`MYLAB.local`) and analyzed AD-related security events.
* **Log Analysis & Threat Hunting:** Used the Wazuh dashboard (Threat Hunting module) to filter and search for specific alerts and raw Windows Event IDs.
* **Attack Simulation:** Performed hands-on Red Team actions (Persistence via user creation, Defense Evasion via log clearing).
* **Vulnerability Management:** Used the Wazuh Vulnerability Detector to find active CVEs on endpoints.
* **Compliance Auditing:** Used Wazuh's Security Configuration Assessment (SCA) to find security misconfigurations.

---

## 🛠️ Tools Used
* **SIEM:** Wazuh (All-in-one installation on host)
* **Virtualization:** VirtualBox
* **Operating Systems:**
    * Ubuntu 24.04 (Host/Attacker/Wazuh Server)
    * Windows Server 2022 (Domain Controller VM)
    * Windows 11 Enterprise (Workstation VM)
* **Security Tools:**
    * Wazuh Agent (on both Windows VMs)
    * Sysmon (for advanced process logging)
    * Windows `net` & `wevtutil` commands

---
## 👣 Steps & Screenshots

### 1. Environment Setup
The lab consists of the Ubuntu Host (running the Wazuh Server) and two Windows VMs (Server & Workstation) on a Bridged network. This allowed all devices to communicate on the same LAN, simulating a real network.

* **Wazuh Server (Host):** `10.63.233.217` (Example IP)
* **Windows Server (DC):** `10.237.42.132` (Example IP)
* **Windows 11 (Workstation):** `10.237.42.133` (Example IP)

> <img width="1920" height="1080" alt="Screenshot from 2025-10-30 16-57-07" src="https://github.com/user-attachments/assets/64a012e3-4062-4e1f-bc8d-b2b073034166" />


### 2. Installing & Configuring Wazuh and Active Directory
Installed the Wazuh all-in-one server on the Ubuntu host. Promoted the Windows Server VM to a Domain Controller for the `MYLAB.local` domain.

> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9e174ca4-99b9-409f-a7f4-070042205fa8" />

>
> *(This is the screenshot showing "AD DS" and "DNS" in Server Manager)*
>
> ### 3. Configuring Endpoints (Sysmon & Agents)
Installed Sysmon and the Wazuh agent on both Windows VMs. The agents were configured to report to the host's IP address. Both agents successfully connected and showed as "Active" in the Wazuh dashboard.

<img width="1920" height="1080" alt="Screenshot from 2025-10-30 23-53-01" src="https://github.com/user-attachments/assets/9cf9483b-a8d6-4283-857e-f239fc2d3b13" />

### 4. Attack Simulation (Red Team)
With the agents active and monitoring, I simulated two attacks against the Domain Controller (`WIN-SERVER-DC`).

**Tactic 1: Persistence (MITRE T1078.002)**
Created a new user (`shadowadmin`) on the Domain Controller to maintain access.


5. Detection & Analysis (Blue Team)

I immediately went to the Wazuh dashboard to hunt for this activity. By navigating to Threat intelligence > Threat Hunting (Events) and setting the time to "Last 15 minutes," I found the alerts.

Detection 1: Log Clearing (Defense Evasion) Wazuh generated a critical alert (Rule ID 63103: "The audit log was cleared") in direct response to the wevtutil command.
<img width="987" height="519" alt="Screenshot from 2025-10-31 21-55-10" src="https://github.com/user-attachments/assets/a99f9152-7e24-4640-8b97-78f3dd5a8587" />
<img width="1920" height="1080" alt="Screenshot from 2025-10-31 23-32-28" src="https://github.com/user-attachments/assets/bd05bd2a-e36f-48f1-86d0-7560943da4c3" />

Detection 2: New User Created (Persistence) Wazuh detected the creation of the new user with Rule ID 60109: "User account created."
<img width="1920" height="1080" alt="Screenshot from 2025-10-31 23-32-34" src="https://github.com/user-attachments/assets/976d0a86-72e7-401c-b5c6-0085882f194b" />

Here is a detailed breakdown of the features shown in your screenshots, plus other key features of Wazuh. This text is perfect for adding to your GitHub README.md file.

📊 Wazuh Feature Analysis

Wazuh is a powerful, open-source Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) platform. My lab explored its key capabilities for proactive and reactive defense.

Based on the agents deployed to my Windows VMs, I analyzed the following modules:

  Vulnerability Detection: This module proactively scans the software installed on the agents and compares the versions against a massive, real-time database of Common Vulnerabilities and Exposures (CVEs).

   What My Lab Showed: My Windows 11 VM was found to have 5 High-Severity vulnerabilities.

  Analysis: By drilling down (as shown in the second screenshot), I identified that the version of Microsoft Teams installed had multiple critical flaws, including a Remote Code Execution (RCE) vulnerability. This provides actionable      intelligence to an IT team: "Update Microsoft Teams on this machine immediately."
<img width="1920" height="1080" alt="Screenshot from 2025-11-01 00-56-07" src="https://github.com/user-attachments/assets/afabdff5-29f2-4de3-8e20-6a01da146066" />

<img width="1920" height="1080" alt="Screenshot from 2025-11-01 00-57-50" src="https://github.com/user-attachments/assets/774e0717-8526-4c3c-a2bf-1713c35cf629" />

Security Configuration Assessment (SCA) : This module audits the agent's system settings against security "best-practice" checklists, known as benchmarks (in this case, the CIS Benchmark for Windows 11).

  What My Lab Showed: My Windows 11 VM received a failing Score of 26%, with 347 failed security checks.

  Analysis: I could see specific misconfigurations that make an attacker's job easier, such as Ensure "Minimum password length" is set to "14 or more character(s)"... Failed. This shows a SOC analyst how to "harden" systems to reduce the attack surface.
  <img width="1920" height="1080" alt="Screenshot from 2025-11-01 01-07-53" src="https://github.com/user-attachments/assets/a4c12ff3-8b73-442a-9784-07084d7fe345" />

  



