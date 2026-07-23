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

The simulated attack began with a malicious email attachment sent to Fakhri Zamri, who became the initial compromised user. When the attachment was opened, it used remote template injection to retrieve and execute malicious content, resulting in the compromise of the workstation.

After establishing access, the attacker performed local and domain reconnaissance using commands such as whoami, quser, net user, and Get-SmbConnection. These commands were used to identify the current user, active sessions, available accounts, and accessible SMB connections.

The attacker then downloaded several post-exploitation tools to enumerate the Active Directory environment and identify privileged accounts, systems, and potential lateral-movement paths.

During the next stage, the attacker obtained the credentials of the itadmin account, which had Domain Administrator privileges. These credentials were used to move laterally from the compromised workstation to the file-sharing server.

Once access to the file-sharing server was established, the attacker enumerated the available folders and identified important documents. The files were then exfiltrated to the attacker-controlled command-and-control server.

Finally, the attacker encrypted the original documents on the file-sharing server and left a ransom note, completing the ransomware stage of the attack.

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

## Key Findings

1. Initial Compromise Through a Malicious Office Attachment

The investigation identified Fakhri Zamri’s workstation as the initial point of compromise. The user opened a malicious email attachment that used remote template injection to retrieve additional malicious content.

Impact: This allowed the attacker to gain an initial foothold on the workstation.

2. Host and Active Directory Reconnaissance

After compromising the workstation, the attacker executed reconnaissance commands including:

whoami
quser
net user
Get-SmbConnection

The attacker also downloaded post-exploitation tools to enumerate the Active Directory environment.

Impact: This activity allowed the attacker to identify users, active sessions, network resources, privileged accounts, and possible lateral-movement targets.

3. Privileged Account Compromise

The attacker obtained access to the itadmin account, which had Domain Administrator privileges.

Impact: The compromised credentials provided the attacker with elevated access across the domain and enabled further movement within the environment.

4. Lateral Movement to the File-Sharing Server

Using the compromised privileged credentials, the attacker moved from the workstation to the file-sharing server.

Impact: This expanded the compromise from a single endpoint to a critical server containing organisational documents.

5. Sensitive File Discovery and Collection

After accessing the file-sharing server, the attacker enumerated available directories and identified important documents.

Impact: This allowed the attacker to locate data suitable for theft and ransomware extortion.

6. Data Exfiltration to Attacker-Controlled Infrastructure

The investigation found that original documents were transferred from the file-sharing server to an attacker-controlled command-and-control server.

Impact: The incident involved not only file encryption but also data theft, indicating a double-extortion ransomware attack.

7. Ransomware Deployment

The attacker encrypted important documents stored on the file-sharing server and left a ransom note.

Impact: The encryption disrupted access to critical files, while the stolen copies could be used to pressure the victim into paying the ransom.

## MITRE ATT&CK Mapping

````markdown
## MITRE ATT&CK Mapping

| Attack Phase | Tactic | Technique | Technique ID | Investigation Evidence |
|---|---|---|---|---|
| Malicious email delivery | Initial Access | Spearphishing Attachment | `T1566.001` | A malicious Office document was delivered to Fakhri Zamri through email. |
| Malicious attachment opened | Execution | User Execution: Malicious File | `T1204.002` | The initial compromise occurred after the user opened the malicious attachment. |
| Remote template loaded | Defense Evasion | Template Injection | `T1221` | The Office document retrieved malicious content through an external template reference. |
| Command execution | Execution | Command and Scripting Interpreter: Windows Command Shell | `T1059.003` | Commands such as `whoami`, `quser`, and `net user` were executed on the compromised workstation. |
| PowerShell execution | Execution | Command and Scripting Interpreter: PowerShell | `T1059.001` | PowerShell was used to execute commands such as `Get-SmbConnection` and support post-exploitation activity. |
| User enumeration | Discovery | System Owner/User Discovery | `T1033` | The attacker used `whoami` and `quser` to identify the current user and active user sessions. |
| Local account enumeration | Discovery | Account Discovery: Local Account | `T1087.001` | The attacker executed `net user` to enumerate local user accounts. |
| Domain account enumeration | Discovery | Account Discovery: Domain Account | `T1087.002` | Active Directory enumeration tools were used to identify domain accounts and privileged users. |
| SMB connection discovery | Discovery | System Network Connections Discovery | `T1049` | `Get-SmbConnection` was used to identify active SMB connections. |
| Network share enumeration | Discovery | Network Share Discovery | `T1135` | The attacker searched for accessible shared folders and network resources. |
| Domain system enumeration | Discovery | Remote System Discovery | `T1018` | Post-exploitation tools were used to identify systems in the Active Directory environment. |
| Post-exploitation tool download | Command and Control | Ingress Tool Transfer | `T1105` | Multiple post-exploitation and Active Directory enumeration tools were downloaded to the workstation. |
| Credential dumping | Credential Access | OS Credential Dumping | `T1003` | A Mimikatz-based tool was used to extract privileged authentication material, including an NTLM hash. |
| Hash-based authentication | Lateral Movement | Use Alternate Authentication Material: Pass the Hash | `T1550.002` | The attacker used the recovered NTLM hash to authenticate without knowing the plaintext password. |
| Privileged account abuse | Privilege Escalation / Defense Evasion | Valid Accounts: Domain Accounts | `T1078.002` | The compromised `itadmin` Domain Administrator account was used to access the file-sharing server. |
| WMI remote execution | Execution | Windows Management Instrumentation | `T1047` | WMI was used to execute commands remotely on the file-sharing server. |
| DCOM lateral movement | Lateral Movement | Remote Services: Distributed Component Object Model | `T1021.003` | DCOM communication supported remote execution and lateral movement to the file-sharing server. |
| Remote PowerShell execution | Execution | Command and Scripting Interpreter: PowerShell | `T1059.001` | Encoded PowerShell was executed remotely to download or reconstruct the next-stage payload. |
| Payload deployment | Command and Control | Ingress Tool Transfer | `T1105` | An additional executable payload was transferred or reconstructed on the file-sharing server. |
| Payload masquerading | Defense Evasion | Masquerading: Match Legitimate Resource Name or Location | `T1036.005` | The malicious executable was named `explorer.exe` to resemble the legitimate Windows process. |
| Folder enumeration | Discovery | File and Directory Discovery | `T1083` | The attacker enumerated folders and documents stored on the file-sharing server. |
| Document collection | Collection | Data from Local System | `T1005` | Important documents were identified and collected from the compromised file-sharing server. |
| Data exfiltration | Exfiltration | Exfiltration Over C2 Channel | `T1041` | Original documents were transferred to an attacker-controlled command-and-control server. |
| Ransomware deployment | Impact | Data Encrypted for Impact | `T1486` | Important documents on the file-sharing server were encrypted and a ransom note was created. |

### Attack Chain Summary

```text
T1566.001 – Spearphishing Attachment
        ↓
T1204.002 – User Execution: Malicious File
        ↓
T1221 – Template Injection
        ↓
T1059.003 / T1059.001 – Command and PowerShell Execution
        ↓
T1033 / T1087 / T1049 / T1135 – System and Domain Discovery
        ↓
T1105 – Post-Exploitation Tool Transfer
        ↓
T1003 – OS Credential Dumping
        ↓
T1550.002 – Pass the Hash
        ↓
T1078.002 – Valid Domain Account
        ↓
T1047 – WMI Remote Execution
        ↓
T1059.001 – Remote PowerShell Execution
        ↓
T1105 – Payload Transfer
        ↓
T1083 / T1005 – File Discovery and Collection
        ↓
T1041 – Data Exfiltration
        ↓
T1486 – Data Encrypted for Impact
```

> **Note:** This MITRE ATT&CK mapping is based on evidence collected during a controlled cybersecurity lab simulation. Techniques were mapped only where supporting forensic evidence was identified.
````


## Indicator of Compormise

## Conclusion 
