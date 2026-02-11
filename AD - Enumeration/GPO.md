
#gpo

Get list of GPO in current domain
```
Get-DomainGPO
Get-DomainGPO | select displayname
Get-DomainGPO -ComputerIdentity dcorp-student1
```

* Get GPO(s) which use Restricted Groups or groups.xml for interesting users
```
Get-DomainGPOLocalGroup
```

* Get users which are in a local group of a machine using GPO
```
Get-DomainGPOComputerLocalGroupMapping -ComputerIdentity
dcorp-student1
```

* Get machines where the given user is member of a specific group
```
Get-DomainGPOUserLocalGroupMapping -Identity student1 -
Verbose
```

