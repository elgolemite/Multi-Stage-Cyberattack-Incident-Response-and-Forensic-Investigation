# Multi-Stage-Cyberattack-Incident-Response-and-Forensic-Investigation
This is NEXSEC CTF 2025 final challenge

`Disclaimer: This investigation was conducted in a controlled cybersecurity lab simulation for educational purposes. No real production systems or organisations were affected.`

## Overview

This project simulates a multi-stage attack targeting two systems: a Windows workstation and a file-sharing server.

Memory forensic analysis identified Fakhri Zamri as the initial compromised user. The incident began when the user opened a malicious email attachment containing a remote template injection technique, which resulted in the compromise of the workstation.

After gaining access, the simulated attacker performed basic system and user reconnaissance using commands such as whoami, quser, net user, and Get-SmbConnection. The attacker also downloaded multiple post-exploitation tools to enumerate the Active Directory environment and gather information about users, systems, and network resources.

The attacker later obtained the credentials of the itadmin account, which had Domain Administrator privileges, and used them to perform lateral movement to the file-sharing server.

After compromising the file-sharing server, the attacker enumerated the available folders, identified important documents, and exfiltrated the original files to a command-and-control server. The attacker then encrypted the documents and left a ransom note on the compromised system.

The investigation reconstructs the full attack chain, from initial access and reconnaissance to credential compromise, lateral movement, data exfiltration, and ransomware deployment.

## Tools Used

|Tools|Purpose|
|-|-|
|Wireshark|Analysed network traffic associated with the compromised workstation and file-sharing server.|
|dnSpy|Decompiled and reverse-engineered malicious .NET executables to inspect their code and behaviour.|
|Sfextractor|Extracted embedded components from packed or self-extracting executable files for further analysis.|
|Openssl|Decrypted encrypted data extracted from network traffic using the recovered encryption key and parameters.|
|Splunk|Correlated security events and reconstructed the attack timeline across the affected systems.|
|Autopsy|Examined forensic disk images and recovered files, system artefacts, user activity, and other evidence from the compromised machines.|
|Oletools|Analysed the malicious Office document for VBA macros, embedded objects, external links, and other suspicious behaviour.|
|Cyberchef|Decoded and deobfuscated encoded commands, scripts, strings, and network data.|
|Eric Zimmerman’s Tools|Used AmcacheParser to examine Amcache artefacts and corroborate the presence or possible execution of malicious tools.|
|CAPA |Identified the likely capabilities and behaviours of the analysed malware samples.|

## Attack Flow

Attack Chain
Malicious email attachment
        ↓
Remote template injection
        ↓
Workstation compromise
        ↓
System and user reconnaissance
        ↓
Active Directory enumeration
        ↓
Domain Administrator credential compromise
        ↓
Lateral movement to file-sharing server
        ↓
Folder and document enumeration
        ↓
Data exfiltration to C2 server
        ↓
File encryption and ransom note

## Investigation Highlights
### 1. Initial Compromise



### 2. Reconnaissance and Credential Access



### 3. Lateral Movement



### 4. Exfiltration and Ransomware



## MITRE ATT&CK Mapping

| Attack Phase | Technique | ID | Evidence |
|---|---|---|---|
| Malicious email delivery | Spearphishing Attachment | `T1566.001` | Malicious Office document delivered through email. |
| Attachment execution | User Execution: Malicious File | `T1204.002` | The victim opened the malicious document. |
| Initial compromise | Template Injection | `T1221` | The document retrieved a remote template. |
| Command execution | PowerShell | `T1059.001` | PowerShell supported reconnaissance and payload execution. |
| Environment discovery | Account and Network Discovery | `T1087`, `T1135`, `T1018` | Commands and tools enumerated users, shares, and systems. |
| Tool download | Ingress Tool Transfer | `T1105` | Post-exploitation tools were downloaded. |
| Credential theft | OS Credential Dumping | `T1003` | A Mimikatz-based tool extracted NTLM credential material. |
| Lateral movement | Pass the Hash | `T1550.002` | The stolen NTLM hash was used for authentication. |
| Remote execution | Windows Management Instrumentation | `T1047` | WMI/DCOM executed commands on the file server. |
| File discovery | File and Directory Discovery | `T1083` | Important server documents were enumerated. |
| Data theft | Exfiltration Over C2 Channel | `T1041` | Documents were transferred to attacker infrastructure. |
| Ransomware | Data Encrypted for Impact | `T1486` | Documents were encrypted and a ransom note was created. |


> **Note:** This MITRE ATT&CK mapping is based on evidence collected during a controlled cybersecurity lab simulation. Techniques were mapped only where supporting forensic evidence was identified.

## Indicator of Compormise

|Type|IOC|
|-|-|
|IP|209.97.175.18|
|Files|xvzpox75.txt, C:\Users\FAKHRI~1.ZAM\AppData\Local\Temp\, explorer.exe, C:\Users\Public\, Brainstemo.exe, BrocaArea.ps1, Neurotransmitter.exe|
|Hash|2412b095ddd77acf9f12fb9f27a3b45f (Financial.docx), 21255ae7a8212384c0ae33196b564df7 (xvzpox75.txt), 78f54036cc749baaf0b0f9216d458d19 (explorer.exe), 8182a667355dce657514b279e43da9f7 (BrocaArea.ps1), db89ec570e6281934a5c5fcf7f4c8967 (Brainstemo.exe), 621383cb58c85c414a3c1e791a2c06a2 (Cereberum.ps1), e930b05efe23891d19bc354a4209be3e (Neurotransmitter.exe)|
|Domain|coorp.local|
|URL|https://github.com/TomatoTerbang/fluffy-umbrella/raw/refs/heads/main/Reference.docm, https://raw.githubusercontent.com/TomatoTerbang/fluffy-umbrella/refs/heads/main/rainingdroplets.txt, https://raw.githubusercontent.com/TomatoTerbang/fluffy-umbrella/refs/heads/main/windyseasons.txt|


