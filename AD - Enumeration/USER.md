
Load :
```
. \Find-PSRemotingLocalAdminAccess.ps1
```

* Find all machines on the current domain where the current user has local admin access
```
Find-LocalAdminAccess -Verbose
Find-PSRemotingLocalAdminAccess -verbose
```

We can use tools below for the same : 
```
Find-WMILocalAdminAccess.ps1 
Find-PSRemotingLocalAdminAccess.ps1
```

If we find any computer with local admin access we can EnterPSSession into that :
```
Enter-PSSession dcorp-adminsrv
```

* Find computers where a domain admin (or specified user/group) has sessions:
```
Find-DomainUserLocation -Verbose
Find-DomainUserLocation -UserGroupIdentity "RDPUsers"
```

* Find computers where a domain admin session is available and current user has admin access (uses Test-AdminAccess).
```
Find-DomainUserLocation -CheckAccess
```

* Find computers (File Servers and Distributed File servers) where a domain admin session is available.
```
Find-DomainUserLocation -Stealth
```

* List sessions on remote machines (https://github.com/Leo4j/Invoke-SessionHunter)
```
Invoke-SessionHunter -FailSafe
```

Above command doesn’t need admin access on remote machines. Uses Remote Registry and queries HKEY_USERS hive.

* An opsec friendly command would be (avoid connecting to all the targetmachines by specifying targets)
```
Invoke-SessionHunter -NoPortScan -Targets C:\AD\Tools\servers.txt
```

Always exclude DC

