# Cap - CTF Writeup

Target: Cap (Linux)

Assessment Type: Authorized Penetration Test

Machine Link: https://app.hackthebox.com/machines/Cap

Date Completed: 07/03/2025

---

## 1. Reconnaissance

Initial port scanning revealed three open TCP ports:

- FTP (port 21)  
- SSH (port 22)  
- HTTP (port 80)

---

## 2. Service Enumeration

### 2.1 Web Application & Security Snapshot

The HTTP service hosts a web application that performs a “Security Snapshot” function. Accessing the web app triggers a redirection to a URL path of the format `/data/[id]`, where `[id]` corresponds to an identifier for the scan.

Exploring the application, it was discovered that it is possible to access other users' security scan data by manipulating the `[id]` parameter, indicating insufficient access control.

### 2.2 Sensitive Data Extraction from PCAP

Among the accessible scan data, a PCAP file with ID `0` was identified containing sensitive network captures. Analysis of this PCAP revealed that FTP credentials were transmitted in cleartext.

### 2.3 Credential Reuse Across Services

Using the FTP credentials recovered from the PCAP, login to the SSH service was successful with the same username and password, confirming credential reuse on the machine.

---

## 3. Exploitation & Privilege Escalation

### 3.1 User Access

Authenticated access to the `nathan` user account was obtained via SSH using the recovered credentials.

### 3.2 Root Privilege Escalation

Further enumeration identified a binary with special capabilities set, located at `/usr/bin/python3.8`. This binary was misconfigured, allowing privilege escalation to root by abusing its permissions.

---

## 4. Post-Exploitation

### 4.1 Flag Retrieval

- **User Flag:** Found in `nathan`'s home directory.  
- **Root Flag:** Found in the `/root` directory.

Both flags were successfully obtained.

---

## Conclusion

The *Cap* machine showcases risks stemming from weak access controls and credential management, combined with misconfigured system binaries. Unauthorized access to sensitive scan data allowed extraction of FTP credentials, which were reused for SSH login. A misconfigured Python binary with elevated privileges permitted escalation to root.

### Recommendations:

- Implement strict access control on user-specific data endpoints.  
- Avoid cleartext protocols like FTP; use encrypted alternatives.  
- Enforce unique, strong passwords per service to prevent credential reuse.  
- Regularly audit binaries with special capabilities and restrict permissions.
