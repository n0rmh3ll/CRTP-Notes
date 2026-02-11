
For enumeration we can use the following tools :
   * ADModule - The ActiveDirectory PowerShell module
   * PowerView 
   * SharpView

### Using PowerView
#powerview #enum

* Load powerView :
```
powershell -ExecutionPolicy Bypass
```

```
. C:\ProgramData\PowerView.ps1
```
or 
```
Import-Module C:\ProgramData\PowerView.ps1
```

* Get current domain
```
Get-Domain
```

* Get object of another domain
```
Get-Domain -Domain moneycorp.local
```

* Get domain SID of current domain
```
Get-DomainSID
```

* Get domain policy for the current domain
```
Get-DomainPolicyData
```

* Get domain controllers for the current domain
```
Get-DomainController
```

* Get domain controllers for another domain
```
Get-DomainController -Domain moneycorp.local
```

* Get a list of users in the current domain
```
Get-DomainUser 
Get-DomainUser | select samaccountname
Get-DomainUser | select samaccountname,logonCount 
Get-DomainUser -Identity student1
```

* List machine accounts in the domain
```
 Get-DomainUser * -spn | select samaccountname
```

* Get list of all properties for users in the current domain
```
Get-DomainUser -Identity student1 -Properties * 
Get-DomainUser -Properties samaccountname,logonCount
```

* Search for a particular string in a user's attributes:
```
Get-DomainUser -LDAPFilter "Description=*built*" | Select name,Description
```

* Get a list of computers in the current domain:
```
Get-DomainComputer | select Name 
Get-DomainComputer | select dnshostname,logonCount 
Get-DomainComputer -OperatingSystem "*Server 2022*" 
Get-DomainComputer -Ping
```

* Get all the groups in the current domain:
```
Get-DomainGroup | select Name 
Get-DomainGroup -Domain moneycorp.local
```

* Get all groups containing the word "admin" in group name
```
Get-DomainGroup *admin* | select Name 
Get-DomainGroup *admin* -Domain moneycorp.local | select Name 
```

* Get all the members of the Domain Admins group :
```
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
```

* Get the group membership for a user: 
```
Get-DomainGroup -UserName "student1"
```

* List all the local groups on a machine (needs administrator privs on non-dc machines) :
```
Get-NetLocalGroup -ComputerName dcorp-dc
```

* Get members of the local group "Administrators" on a machine (needs administrator privs on non-dc machines) :
```
Get-NetLocalGroupMember -ComputerName dcorp-dc -GroupName Administrators
```

* Get actively logged users on a computer (needs local admin rights on the target):
```
Get-NetLoggedon -ComputerName dcorp-adminsrv
```

* Get locally logged users on a computer (needs remote registry on the target - started by-default on server OS) :
```
Get-LoggedonLocal -ComputerName dcorp-adminsrv
```

* Get the last logged user on a computer (needs administrative rights and remote registry on the target:
```
Get-LastLoggedOn -ComputerName dcorp-adminsrv
```

* Find shares on hosts in current domain :
```
Invoke-ShareFinder -Verbose
``` 

*  Find sensitive files on computers in the domain : 
```
Invoke-FileFinder -Verbose
``` 

* Get all fileservers of the domain:
```
Get-NetFileServer
```

Alternatively we can use tool to enumerate shares such as `PowerHuntShares`

```
https://github.com/NetSPI/PowerHuntShares
```

Now let's find a file share where studentx has Write permissions ::

First get all the hosts to scan
```
Get-DomainComputer | select -ExpandProperty dnshostname 
```
save this hosts (Excluding DC) to servers.txt

Load the module
```
Import-Module c:\AD\Tools\PowerHuntShares.psm1
```

🚨 Remember to Exclude DC before running

Run :
```
Invoke-HuntSMBShares -NoPing -OutputDirectory C:\AD\Tools -HostList C:\AD\Tools\servers.txt
```