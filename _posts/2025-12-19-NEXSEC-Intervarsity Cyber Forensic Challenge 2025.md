# NEXSEC 2025: Intervarsity Cyber Forensics Challenge

Last week, I participated in **NEXSEC 2025 – Intervarsity Cyber Forensics Challenge**, and it was easily one of the most meaningful CTFs I have joined so far. Our team managed to get into the top 10 for the preliminary stage, and unlike traditional CTFs, where challenges are released immediately, the final stage focused heavily on **DFIR**. We were given evidence first and questions later, forcing us to investigate the incident like real analysts instead of solving isolated challenges.

---

## Challenge Format: DFIR Over Flags

The competition ran for **two days**.

- **Day 1**:  
  We were given:
  - Two virtual machines (Workstation & File Share Server)
  - One `pcapng` file
  - Splunk IP address

  No questions. No hints. Just an incident.
- **Day 2**:  
  The questions were released only after the investigation phase. By then, we were expected to already understand the incident timeline, attacker techniques, and impact.

This format encouraged proper evidence handling, hypothesis building, and team correlation rather than guesswork.

---

## My Focus: File Share Server Investigation

While my teammates focused on analysing the workstation VM and working with Splunk, I started my investigation on the **File Share server**, as it was likely the system that suffered the final impact.

Early in the analysis, I found:
- A **ransom note**
- Multiple files in the `Documents` directory encrypted with the **`.anon`** extension

This strongly indicated a ransomware incident, so my goal became understanding **how the encryption happened** and **what executed it**.

---

## Suspicious Binary: `explorer.exe`

During file system triage, I discovered a suspicious binary:

```
C:\Users\Public\explorer.exe
```

Although the filename looked legitimate, its location was not. This immediately suggested a **masquerading malware binary**. Further analysis later confirmed this file acted as a **C2 backdoor**, used to receive additional payloads from the attacker.

---

## When Splunk Was Unavailable: Event Log Analysis

At the beginning of the competition, I faced issues accessing Splunk. Instead of waiting, I exported and analysed **Windows Event Logs** manually, focusing on:
- PowerShell Operational logs
- Security Event logs

This allowed me to trace attacker activity directly from the system without relying on centralised logging tools.

---

## Initial Access and Lateral Movement (From Logs)

By correlating PowerShell and Security logs, I identified that:
- The attacker initially compromised the workstation
- Credentials were stolen
- A **Pass-the-Hash attack** was used to move laterally
- The malicious `explorer.exe` binary was dropped onto the File Share server during this movement

These findings later matched perfectly with my teammates’ Splunk analysis and workstation findings.

---

## How We Identified the Encryption Malware from the PCAP

One of the biggest questions was how the files were encrypted. To answer this, we analysed the provided **`pcapng` file** together as a team.

From the network traffic, we noticed that after the backdoor established communication with its C2 server, it opened a **separate TCP connection on port 7219**. Unlike normal HTTPS traffic, this connection transferred large encrypted data blobs.

By reconstructing these TCP streams, we discovered that:
- The server was sending **encrypted .NET DLL payloads** to the File Share server
- Each payload was prefixed with its length, then encrypted using **AES-CBC**
- The AES key and IV were hardcoded inside `explorer.exe`

Using this information, we decrypted the traffic and extracted two DLL files:
- One DLL responsible for **data exfiltration**
- One DLL responsible for **file encryption (ransomware)**

This explained how the attacker modularised their attack: the backdoor handled communication, while separate DLLs handled specific tasks like encryption.

---

## Understanding the Encryption Behaviour

After extracting the ransomware DLL, we analysed it using **ILSpy**. In simplified terms, the encryption process worked as follows:
1. The malware recursively scanned directories for files
2. Each file was encrypted using **AES**
3. The AES key was then encrypted using **RSA**
4. The encrypted files were saved with the `.anon` extension
5. A ransom note was dropped after encryption was completed

This confirmed that the File Share server was the final impact point of the attack.

---

## Finding the Private Key and Decrypting the Files

After understanding how the ransomware encrypted the files, the next challenge was determining whether **file recovery was possible**.

By further analyzing the **C2 infrastructure**, we discovered that the attacker’s server exposed several poorly secured endpoints. One of these endpoints allowed us to extract backend data related to infected hosts. Within this data, we identified an **RSA private key** associated with the infected File Share server.

This private key corresponded directly to the **public key embedded inside the ransomware DLL**. Since the ransomware used a hybrid encryption scheme (RSA + AES), possessing the private key allowed us to reverse the encryption process.

Using the recovered private key:
1. We decrypted the RSA-encrypted AES key stored at the beginning of each `.anon` file
2. The recovered AES key was then used to decrypt the file contents
3. Original files were successfully restored, confirming full recovery was possible

This step completed the investigation by not only explaining the attack, but also demonstrating **successful incident recovery**, which is a critical part of DFIR.

---

## Event Correlation and Teamwork

Throughout the investigation, I continuously correlated my **event log findings** with my teammates’ results from:
- Splunk
- PCAP analysis
- File system analysis
- Reverse engineering

This allowed us to build a single, accurate attack timeline instead of working in silos.

---

## Afterthoughts

One important realization from this challenge was that by analyzing **Windows Event Logs**, I could already identify most of the PowerShell commands that later appeared clearly in Splunk. This gave me first-hand experience in understanding that investigative tools do not replace fundamentals — they enhance them. Strong log analysis skills make investigations possible even without advanced tooling, while tools like Splunk make the process faster and more efficient.

---

## Final Thoughts

NEXSEC 2025 felt less like a typical CTF and more like a real-world incident response engagement. This challenge reinforced why I enjoy digital forensics and incident response so much — not just solving problems, but understanding the story behind an attack. If you’re interested, I highly recommend reading my teammates’ write-ups (as they did most of the reverse engineering):
- https://qiaonpc.github.io/post/finals/
- https://fyr3p4w.gitbook.io/fyr3p4w-blog/ctfs/nexsec-2025-intervarsity-cyber-forensics-challenge
