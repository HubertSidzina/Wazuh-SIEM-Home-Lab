# SOC Home Lab: Threat Detection & Automated Response in Wazuh SIEM

## 1. Architecture & Project Goal
The goal of this project was to build a functional SOC lab environment from scratch to practice log ingestion, custom correlation rule engineering, incident investigation, and automated threat response (**SOAR**).

* **Virtualization:** Oracle VM VirtualBox (Bridged Adapter)
* **SIEM Server:** Wazuh Server (Manager, Indexer, Dashboard)
* **Monitored Host:** Windows 11 + Wazuh Agent + Sysmon

---

## 2. Threat Simulation & Investigation Case Studies

### Case Study 1: Detecting Obfuscated PowerShell (`-EncodedCommand`) & Decoding
* **Simulation:** Executed a hidden PowerShell process with a Base64-encoded command (`MITRE T1059.001`).
* **Detection & Analysis:** Captured the full command line (`commandLine`) and process tree (`parentImage`) via **Sysmon (Event ID 1)** integrated with Wazuh. Decoded the payload in **CyberChef** revealing `whoami; hostname`.

![](https://github.com)

---

### Case Study 2: File Integrity Monitoring (FIM)
* **Simulation:** Created, modified, and deleted a ransom note file (`okup.txt`) inside the monitored directory `C:\Bank`.
* **Detection:** Configured the `syscheck` module in `realtime="yes"` mode, capturing file changes and the user account performing the operations in real time.

![](https://github.com)

---

### Case Study 3: Brute-Force Detection & Automated IP Blocking (Active Response)
* **Simulation:** Conducted multiple failed network logon attempts from an external host (`Event ID 4625`).
* **Response (SOAR):** Configured **Active Response** (`route-null.exe`) triggered by `Rule ID 60122` (*Multiple Windows Logon Failures*), automatically blocking the attacker's IP address for 600 seconds.

![](https://github.com)

---

## 3. Custom SOC Analytical Dashboard
Built a dedicated view in **Wazuh Dashboard** displaying a counter of high-severity incidents (`rule.level >= 10`) and a breakdown of top triggered alerts.

![](https://github.com)
