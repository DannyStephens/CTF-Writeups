# Clarke Confectionary - CTF Writeup

**Target:** Internal Network (Windows)  
**Assessment Type:** Authorized Internal Penetration Test  
**Machine Scope:** 192.168.20.0/24  
**Date Completed:** 17/06/2024  

---

## 1. Reconnaissance

Initial Nmap scanning revealed several active hosts with various open ports:

- **192.168.20.12** (Windows Server 2008)  
  - Ports: 21 (FTP), 23 (Telnet), 135 (MSRPC), 445 (SMB)  
- **192.168.20.9** (Client machine)  
  - Ports: 21 (FTP), 80 (HTTP), 445 (SMB)  
- **192.168.20.8** (Windows 10 client)  
  - Ports: 135 (MSRPC), 139 (NetBIOS), 445 (SMB), 3389 (RDP)

---

## 2. Service Enumeration

### 2.1 Vulnerability Discovery via OpenVAS

OpenVAS scanning on the above hosts revealed a critical vulnerability (MS17-010 / EternalBlue) in the SMB service running on `192.168.20.12`. The system was missing essential patches and running outdated Windows Server 2008.

### 2.2 Exploitable Attack Surface

Nmap and OpenVAS combined indicated that `192.168.20.12` had:
- Outdated SMBv1 protocols
- Multiple exploitable ports (FTP, Telnet, SMB)

This host was selected as the initial target for exploitation.

---

## 3. Exploitation & Privilege Escalation

### 3.1 Remote Code Execution via EternalBlue

**Tool Used:** Metasploit  
Exploit: `windows/smb/ms17_010_eternalblue`  
Payload: `reverse_tcp`

Metasploit was used to exploit the vulnerability, providing SYSTEM-level shell access to the server.

### 3.2 Post-Exploitation

- Dumped password hashes using `hashdump`
- Gathered system info and installed a persistent backdoor
- Cleared logs with `clearev` to cover tracks

---

## 4. Lateral Movement

### 4.1 Password Cracking

**Tool Used:** [Hashes.com](https://hashes.com)  
Password hashes were uploaded and cracked, yielding valid plaintext credentials for domain users.

### 4.2 Remote Desktop Access

Using the cracked credentials, RDP access to `192.168.20.9` was successful. Admin privileges were obtained.

Attempts to access `192.168.20.8` failed due to incorrect credentials and unsuccessful exploits.

---

## 5. Post-Exploitation

### 5.1 Administrative Control

With full access to `192.168.20.9`, the attacker could:
- Install or remove software
- Reconfigure server settings
- Shut down or restart systems

### 5.2 Sensitive Data Exposure

The attacker had access to:
- Employee credentials and hashes
- System configuration files
- Full administrative GUI

---

## Conclusion

The engagement revealed significant vulnerabilities in Clarke Confectionary’s network, primarily due to:

- Use of legacy systems (Windows Server 2008)
- Lack of critical security patches (MS17-010)
- Poor credential management and reuse

### Recommendations:

- **Patch Management:** Upgrade legacy systems and apply updates regularly.
- **Credential Policies:** Enforce unique, strong passwords per service.
- **Monitoring:** Deploy real-time network and host-based intrusion detection.
- **Threat Detection:** Utilize AI-driven threat detection for faster response.
- **Incident Response Plan:** Train staff and define clear, legal protocols for breach scenarios.
