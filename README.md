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
|DnSpy|Decompiled and reverse-engineered malicious .NET executables to inspect their code and behaviour.|
|Sfextractor|Extracted embedded components from packed or self-extracting executable files for further analysis.|
|Openssl|Decrypted encrypted data extracted from network traffic using the recovered encryption key and parameters.|
|Splunk|Correlated security events and reconstructed the attack timeline across the affected systems.|
|Auotpsy|Examined forensic disk images and recovered files, system artefacts, user activity, and other evidence from the compromised machines.|
|Oletools|Analysed the malicious Office document for VBA macros, embedded objects, external links, and other suspicious behaviour.|
|Cyberchef|Decoded and deobfuscated encoded commands, scripts, strings, and network data.|
|Eric Zimmerman’s Tools|Used AmcacheParser to examine Amcache artefacts and corroborate the presence or possible execution of malicious tools.|
|CAPA |Identified the likely capabilities and behaviours of the analysed malware samples.|

## Attack Flow

## Key Findings

## MITRE ATT&CK Mapping

## Indicator of Compormise

## Conclusion 
