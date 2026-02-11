

#acl

Access Control List (ACL)

 It is a list of Access Control Entries (ACE) - ACE corresponds to individual
permission or audits access. Who has permission and what can be done
on an object?

• Two types:
– DACL - Defines the permissions trustees (a user or group) have on an object.
– SACL - Logs success and failure audit messages when an object is accessed.

* Get the ACLs associated with the specified object
```
Get-DomainObjectAcl -SamAccountName student1 -ResolveGUIDs
```
or Group

```
Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs
```

* Get the ACLs associated with the specified prefix to be used for search
```
Get-DomainObjectAcl -SearchBase "LDAP://CN=Domain
Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -
Verbose
```

* Search for interesting ACEs
```
Find-InterestingDomainAcl -ResolveGUIDs
```

* Get the ACLs associated with the specified path
```
Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"
```
