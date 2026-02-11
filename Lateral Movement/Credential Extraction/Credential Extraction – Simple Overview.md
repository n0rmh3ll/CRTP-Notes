
### **Credential Extraction – Simple Overview**

- **Goal:** Steal valid credentials to enable **lateral movement** and privilege escalation
    
- **LSASS Memory**
    - Stores active logon credentials
    - Extracts NTLM hashes, Kerberos tickets, keys
    - High value but **heavily monitored**
        
- **SAM Hive (Registry)**
    - Contains **local user password hashes**
    - Used to move between machines with same local creds
        
- **LSA Secrets / SECURITY Hive**
    - Stores **service account passwords**
    - Cached domain credentials
        
- **DPAPI-Protected Data (Disk)**
    - Windows Credential Manager
    - Browser passwords & cookies
    - Certificates and cloud/Azure tokens

**Summary:**

> Attackers extract credentials from memory, registry, and disk to move laterally using legitimate access instead of exploits.
