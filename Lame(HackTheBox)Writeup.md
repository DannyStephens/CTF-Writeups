# Lame - HackTheBox Writeup

**Target:** Lame (Linux)  

**Assessment Type:** Authorized Penetration Test  

**Machine Link:** [https://app.hackthebox.com/machines/Lame](https://app.hackthebox.com/machines/Lame)

**Date Completed:** 09/03/2025 



---

## 1. Reconnaissance

A full TCP port scan was conducted to identify exposed services. The scan revealed the following open ports:

- **21/tcp** – FTP (vsftpd 2.3.4)  
- **22/tcp** – SSH  
- **139/tcp** – NetBIOS Session Service  
- **445/tcp** – SMB over TCP

---

## 2. Service Enumeration

### 2.1 FTP – VSFTPD 2.3.4

The FTP service was running VSFTPD version 2.3.4, which is known to contain a backdoor introduced by a malicious developer. The exploit typically opens a shell on a secondary port when a specific username pattern is used.

In this case, although the software version matched the known vulnerable release, no remote shell could be obtained. This suggests that while the backdoor may be present, it is likely blocked by a host-based firewall.

### 2.2 SMB – Samba 3.0.20

The Samba service was running version 3.0.20, which is vulnerable to a critical remote code execution flaw: **CVE-2007-2447**. This vulnerability allows unauthenticated attackers to execute arbitrary commands via user mapping features.

---

## 3. Exploitation

The Samba vulnerability was exploited using a known technique targeting the usermap script misconfiguration. This resulted in immediate root access to the system, without requiring credentials or additional privilege escalation.

---

## 4. Post-Exploitation

### 4.1 Privilege Verification

Upon successful exploitation, access was confirmed to be at the **root** level.

### 4.2 Flag Retrieval

- The **user flag** was located in the home directory of the `makis` user.
- The **root flag** was located in the `/root` directory.

Both were accessed and retrieved without issue.

### 4.3 Internal Network Review

While investigating the internal state of the system, it was confirmed that the backdoor associated with VSFTPD did in fact activate a listener on the expected port. However, external connections to that port were being filtered — most likely by a local firewall configuration.

---


## Conclusion

The *Lame* host is a textbook example of how unpatched and misconfigured services can lead to full system compromise. The vulnerable version of Samba provided unauthenticated root access. Although the VSFTPD backdoor was present, host-based firewall restrictions prevented its exploitation externally.

### Recommendations:

- Immediately update all services to secure, supported versions.
- Restrict external access to administrative services (SMB, FTP) using network segmentation and firewall rules.
- Regularly audit and monitor exposed services for known vulnerabilities.
- Apply defense-in-depth by hardening service configurations and enabling intrusion detection systems.


