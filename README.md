# Windows Credential Harvesting (Defensive & Red-Team Awareness Overview)

Credential security is one of the most critical components of Windows enterprise defense.  
This README provides a high-level overview of how red-teamers assess weaknesses in credential storage and authentication flows using commonly referenced tools and techniques.  
All details are strictly defensive, focusing on understanding risk—not performing exploitation.

---

## 🔐 Overview of Credential Harvesting
Credential harvesting is a phase in an attack simulation where security teams evaluate how authentication secrets are stored, protected, and exposed across Windows systems.  
Rather than focusing on execution, the goal is to identify weak configurations, legacy mechanisms, and oversight in identity security that real attackers could abuse.

Red-teamers analyze:
- How credentials reside in memory  
- How the system stores local password hashes  
- How domain authentication material is replicated  
- How poorly configured systems expose sensitive identity data  

These findings help defenders harden endpoints, enable modern protections, and build better monitoring around identity compromise.

---

##  Mimikatz 
**Mimikatz** is a well-known tool used to demonstrate how Windows authentication secrets can be accessed if system protections are weak.  
It highlights risks such as:
- Exposed credentials inside **LSASS memory**  
- Insufficient protection of NTLM hashes and Kerberos tickets  
- Missing controls like Credential Guard or LSA protection  

Red-teamers use it to **validate whether an organization’s memory protections and identity controls are properly configured**, helping blue teams improve defenses against credential theft.

---

##  SecretsDump.py 
**SecretsDump.py** from the Impacket framework showcases how authentication data can be extracted remotely or locally from Windows systems when permissions are misconfigured.  
It demonstrates risks around:
- Weak or exposed administrative credentials  
- Legacy authentication protocols  
- Poor segmentation that allows unauthorized access to sensitive registry hives  

Its purpose in simulations is to show **how attackers could read credential material** if access controls are not properly enforced—allowing defenders to patch these weaknesses.

---

## 🗂️ SAM & SYSTEM Registry-Based Credential Exposure
Windows stores local password hashes in the **Security Account Manager (SAM)** database, which is protected by encryption keys stored in the **SYSTEM** registry hive.  
If an attacker gains elevated privileges, they may attempt to copy these registry files or memory dumps to reconstruct the authentication data offline.  

Red-team assessments examine:
- Whether endpoint hardening prevents unauthorized registry hive access  
- Whether EDR tools alert on unusual registry dumping attempts  
- Whether segmentation and privilege separation limit such exposure  

This evaluation is essential for verifying that local credential stores are not left weak or unmonitored.

---

## 🛡️ Why This Matters for Defenders
Understanding how these tools and techniques *could* be abused helps organizations:
- Strengthen identity and endpoint hardening  
- Enable protections like Credential Guard, LSA Protection, and Secure Boot  
- Improve detection for LSASS access, registry hive dumping, and credential extraction attempts  
- Enforce least-privilege administrative models  
- Reduce the risk of lateral movement and privilege escalation  

---

## 📌 Summary
This repository explains credential harvesting from a defender’s perspective.  
By understanding how Windows stores and protects sensitive identity data—and how common tools reveal weaknesses—organizations can implement stronger controls, detect abuse earlier, and significantly reduce their attack surface.

