
## **1️⃣ AS‑REQ (Client → KDC)**

**Goal:** Get a TGT  
**What’s used:** Username + password (or pre‑auth)

### Attacks

- **Username Enumeration** – different errors = valid users
    
- **AS‑REP Roasting** – no pre‑auth → crack user password offline
    
- **Password Spraying** – common passwords across many users
    
- **Weak crypto allowed** – RC4 downgrade
    

---

## **2️⃣ AS‑REP (KDC → Client)**

**Goal:** Receive TGT + session key  
**Encrypted with:** User’s password hash

### Attacks

- **Offline password cracking** – crack AS‑REP
    
- **Encryption downgrade abuse** – weaker cipher = faster crack
    
- **Replay (rare)** – only if timestamps/misconfigs exist
    

---

## **3️⃣ TGS‑REQ (Client → TGS)**

**Goal:** Request service ticket  
**Uses:** TGT

### Attacks

- **Kerberoasting** 🔥 – crack service account passwords
    
- **SPN Enumeration** – find high‑value services
    
- **Pass‑the‑Ticket (PTT)** – reuse stolen TGT
    

---

## **4️⃣ TGS‑REP (TGS → Client)**

**Goal:** Receive service ticket  
**Encrypted with:** Service account hash

### Attacks

- **Silver Ticket** 🥈 – forge service tickets (service hash)
    
- **Golden Ticket** 👑 – forge TGTs (krbtgt hash)
    
- **Offline cracking** – weak service account passwords
    

---

## **5️⃣ Service Access (Client → Server)**

**Goal:** Access resource  
**Uses:** Service ticket

### Attacks

- **Pass‑the‑Ticket (PTT)** – authenticate without password
    
- **Delegation Abuse**
    
    - Unconstrained
        
    - Constrained
        
    - RBCD
        
- **Session hijacking** – stolen tickets from LSASS
    
- **Kerberos relay** – rare but possible