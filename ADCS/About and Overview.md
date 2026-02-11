
Active Directory Certificate Services (**AD CS**) is a Microsoft server role that functions as a **Public Key Infrastructure (PKI)** within an Active Directory forest. It provides a framework for issuing and managing digital certificates used for identity verification and data security. 
### **Core Components & Definitions**

- **CA (Certification Authority):** The central server (either a Domain Controller or a standalone server) responsible for issuing and managing certificates.
- **Certificate:** A digital document issued to a user or machine used for authentication, encryption, and digital signatures.
- **CSR (Certificate Signing Request):** An application made by a client to the CA to request a new certificate.
- **Certificate Template:** A set of rules and settings that defines what a certificate can do, including who can enroll for it and how long it lasts.
- **EKU OIDs (Extended Key Usage Object Identifiers):** Specific codes within a template that define the certificate's purpose, such as **Client Authentication** or **Smart Card Logon**.
### **AD CS Enrollment Process Breakdown**

The typical workflow for obtaining a certificate is as follows:

1. **Key Generation**: The client generates a public/private key pair.
2. **CSR Submission**: The client sends a **Certificate Signing Request (CSR)** to an **Enterprise Certification Authority (CA)** server.
3. **Template Validation**: The CA checks if the requested **Certificate Template** exists and if the user has the necessary **enrollment permissions** to request it.
4. **Certificate Issuance**: If approved, the CA generates a certificate and signs it using its own **CA private key**.  
5. **Installation**: The client stores the certificate in the Windows Certificate store and can now use it for tasks defined by its **EKU OIDs** (Extended Key Usage Object Identifiers), such as **Client Authentication** or **Smart Card Logon**.

### Diagram

```
      [ CLIENT ]                               [ ENTERPRISE CA ]
    (User/Machine)                             (Domain 1 or 2)
          |                                           |
          |----(1) Generates Key Pair                 |
          |                                           |
          |----(2) Sends CSR (Template Request) ----->|
          |                                           |----(3) Validates:
          |                                           |    - Template exists?
          |                                           |    - Enrollment perms?
          |                                           |    - Approval needed?
          |                                           |
          |<---(4) Signs & Issues Certificate --------|
          |        (using CA Private Key)             |
          |                                           |
          |----(5) Stores Certificate                 |
          v                                           v
    [ AUTHENTICATION ]                        [ RESOURCE ACCESS ]
    (Across Trust Boundary) ----------------> (Parent/Forest 2)
```


### **Why this matters for Privilege Escalation**

- **Misconfigured Templates:** If a template allows a low-privileged user to request a certificate for a Domain Admin (impersonation), they can gain full control of the domain.
- **EKU Abuse:** If a template has "Client Authentication" enabled and weak enrollment permissions, an attacker can use a forged certificate to log in as any user.
- **Cross-Domain Trusts:**  Certificates can often be used to authenticate across different domains in a forest, bypassing certain security boundaries.


--- 

* We can use the Certify tool (https://github.com/GhostPack/Certify) to enumerate (and for other attacks) AD CS in the target forest:
```
Certify.exe cas
```

* Enumerate the templates :
```
Certify.exe find
```

* Enumerate vulnerable templates :
```
Certify.exe find /vulnerable
```

