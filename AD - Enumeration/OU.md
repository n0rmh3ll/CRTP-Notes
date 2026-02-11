
#ou

* Get OUs in a domain
```
Get-DomainOU
Get-DomainOU | select name 
```

* Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU
```
Get-DomainGPO -Identity "{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}"
```

* List all computers in DevOps OU:
```
(Get-DomainOU -Identity DevOps).distinguishedname | %{Get-DomainComputer -searchBase $_} | select name
```

```
Get-DomainGPO -Identity (Get-DomainOU -Identity StudentMachines).gplink.substring(11,(Get-DomainOU -Identity DevOps).gplink.length-72)
```

* Enumerate GPO applied on DevOps OU :

First enumerate the gplink using :
```
(Get-DomainOU -Identity DevOps).gplink
```
Then use that gplink for :
```
Get-DomainGPO -Identity "{7478F170-6A0C-490C-B355-9E4618BC785D}"
```

This is enumerate the GPO applied on that specific OU

