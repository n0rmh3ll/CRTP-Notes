
# **👥 GROUP — Attacks**

### **If you have GenericAll**

- Add Members
- Remove Members
- Change Ownership
- Reset password of members
- Grant rights to yourself

### **If you have GenericWrite**

- Add/Remove Members

### **If you have WriteDACL**

- Give yourself GenericAll

### **If you have WriteOwner**

- Take ownership → then escalate

---

# **👤 USER — Attacks**

### **If you have GenericAll**

- Reset Password
- Shadow Credentials
- Targeted Kerberoasting
- Modify Logon Script
- Add rights to yourself

### **If you have GenericWrite**

- Reset Password
- Modify Logon Script
- Shadow Credentials

### **If you have AllExtendedRights**

- Reset Password

### **If you have WriteDACL**

- Give yourself GenericAll

### **If you have WriteOwner**

- Take ownership → escalate

---

# **🖥️ COMPUTER — Attacks**

### **If you have GenericAll**

- Reset Machine Password
- Kerberos RBCD
- SPN Jacking
- Shadow Credentials
- Read LAPS Password
- Grant yourself more rights

### **If you have GenericWrite**

- Kerberos RBCD
- SPN Jacking
- Shadow Credentials

### **If you have AllExtendedRights**

- Read LAPS Password

### **If you have WriteDACL**

- Give yourself GenericAll

### **If you have WriteOwner**

- Take Ownership

---

# **🌐 DOMAIN OBJECT — Attacks**

### **If you have GenericAll**

- DCSync
- Read LAPS Password
- Grant rights

### **If you have AllExtendedRights**

- DCSync
- Read LAPS Passwor

### **If you have WriteDACL**

- Grant yourself GenericAll

### **If you have WriteOwner**

- Take Ownership

---

# **🛡️ ADMINSDHOLDER — Attacks**

### **If you have GenericAll**

- Reset password
- Add users to protected groups
- Persistence (SDHolder overwrite)

### **If you have GenericWrite**

- Modify membership
- Reset password

### **If you have WriteDACL**

- Give yourself GenericAll

### **If you have WriteOwner**

- Take ownership → persist

---

# **📜 GROUP POLICY OBJECT — Attacks**

### **If you have GenericAll**

- Modify GPO
- Add Local Admin to machines
- Create Scheduled Task (EvilGPO)

### **If you have WriteDACL**

- Give yourself full control

### **If you have WriteOwner**

- Take Ownership

---

# **🔧 SECURITY DESCRIPTOR — Attacks**

### **If you have GenericAll**

- WMI execution
- Remote PowerShell
- Remote Registry
- Grant more rights
### **If you have WriteDACL**

- Give yourself GenericAll

### **If you have WriteOwner**

- Take **Ownership**