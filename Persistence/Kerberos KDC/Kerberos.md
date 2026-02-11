* Kerberos is the basis of authentication in a Windows Active Directory environment.
* Clients (programs on behalf of a user) need to obtain tickets from Key Distribution Center (KDC) which is a service running on the domain controller. These tickets represent the client's credentials.!
* Therefore, Kerberos is understandably a very interesting target of abuse!


## Workflow of Kerberos

```
                        +-----------------+
                        |   KDC / DC      |
+----------+            |  (AS & TGS)     |             +--------------+
|  Client  |            +-----------------+             | App Server   |
|  (C)     |                     |                      |     (S)      |
+----------+                     |                      +--------------+
     |                           |                              |
     |  1. AS-REQ (Username)     |                              |
     +-------------------------->|                              |
     |                           |                              |
     |<--------------------------+                              |
     |  2. AS-REP (TGT, K_c_tgs) |                              |
     |                           |                              |
     |                           |                              |
     |  3. TGS-REQ (TGT, Auth)   |                              |
     +-------------------------->|                              |
     |                           |                              |
     |<--------------------------+                              |
     |  4. TGS-REP (Service Ticket, K_c_s)                      |
     |                           |                              |
     |                           |                              |
     |                                                          |
     |  5. AP-REQ (Service Ticket, Auth)                        |
     +--------------------------------------------------------->|
     |                                                          |
  (Access Granted!)
  
```


## Explanation of Each step :

#### **Step 1: AS‑REQ (Authentication Service Request)**
The process starts when a user logs in. The client sends an **AS‑REQ** to the **Key Distribution Center (KDC)** asking for authentication. The request includes the **username** and a timestamp, and (in normal cases) proof that the user knows their password (pre‑authentication).  
**Why:** The KDC must confirm the user’s identity without the password ever being sent over the network.

#### **Step 2: AS‑REP (Authentication Service Response)**
If the credentials are valid, the KDC responds with an **AS‑REP** containing two important things:

1. A **Ticket Granting Ticket (TGT)** (encrypted with the KDC’s `krbtgt` key)
2. A **session key** (encrypted with a key derived from the user’s password)

The client can decrypt the session key using their password, but **cannot read the TGT**.  
**Why:** The TGT proves the user is authenticated and can be reused to request services without re‑entering the password.

#### **Step 3: TGS‑REQ (Ticket Granting Service Request)**
When the user wants to access a service (file server, SQL, WinRM, etc.), the client sends a **TGS‑REQ** to the KDC. This request includes the **TGT** and the name of the target service (SPN).  
**Why:** Instead of authenticating again, the user proves their identity using the TGT to request access to a specific service.

#### **Step 4: TGS‑REP (Ticket Granting Service Response)**
The KDC validates the TGT and responds with a **TGS‑REP**, which contains:

1. A **service ticket** (encrypted with the service account’s password hash)
2. A **service session key**

The client still cannot read the service ticket, but can use it.  
**Why:** Only the target service should be able to decrypt the ticket and trust the authentication.

#### **Step 5: Client → Service (Service Authentication)**
The client sends the **service ticket** directly to the target server. The server decrypts it using its own password hash, verifies the client’s identity, and establishes a secure session using the shared session key.  
**Why:** This final step allows **password‑less, secure access** to services and supports **mutual authentication**.


## Why krbtgt Must Be Reset Twice

- AD keeps **current + previous krbtgt key**
- One reset → old key still valid
- Two resets → fully invalidates forged TGTs