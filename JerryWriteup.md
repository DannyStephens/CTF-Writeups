# Jerry - CTF Writeup

**Target:** Jerry (Windows)  

**Assessment Type:** Authorized Penetration Test  

**Machine Link:** https://app.hackthebox.com/machines/Jerry

**Date Completed:** 13/03/2025 

---

## 1. Reconnaissance

A targeted TCP port scan against the host revealed a single open port:

- **8080/tcp** – Hosting a web server

This non-standard HTTP port suggested a custom or embedded web application might be present.

---

## 2. Service Enumeration

### 2.1 Apache Tomcat Web Interface

Upon visiting the web server at port 8080, the landing page identified the service as **Apache Tomcat**, a common Java-based web container.

Further inspection discovered that the **Tomcat Web Application Manager** interface was exposed at the relative path:

/manager/html


### 2.2 Default Credentials & Access

The Web Application Manager required authentication. Using a known default credential combination (`tomcat:s3cret`), access was granted to the management panel. This confirmed the system had not been hardened or reconfigured securely.

---

## 3. Exploitation

### 3.1 Deploying a Malicious WAR File

The Tomcat Manager allows authenticated users to upload `.war` (Web Application Archive) files. By deploying a malicious WAR file containing a reverse shell payload, it was possible to gain code execution on the underlying system.

Once deployed and triggered, the payload granted shell access as a user-level account.

---

## 4. Post-Exploitation

### 4.1 User Flag

The **user flag** was located on the desktop of the compromised user account and was successfully retrieved.

### 4.2 Administrator Access & Root Flag

Since the initial shell had elevated privileges (a common misconfiguration in early HTB Windows machines), the session was already running with **NT AUTHORITY\SYSTEM** permissions.

The **administrator flag** was retrieved directly from the Administrator's desktop, confirming full system compromise.

---

## Conclusion

The *Jerry* machine illustrates common mistakes in web application deployment:

- Exposing management interfaces to unauthenticated users
- Relying on default credentials
- Allowing file uploads without validation or sandboxing
- Running services with excessive privileges

### Recommendations:

- Restrict access to the Tomcat Manager interface via internal IP whitelisting or VPN.
- Remove default credentials and enforce strong, unique passwords.
- Limit user capabilities in deployed web applications.
- Never allow web services to run with SYSTEM-level permissions.
- Monitor and audit web application deployment processes.
