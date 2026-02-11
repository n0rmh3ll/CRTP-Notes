

PowerShell Remoting - Tradecraft

* PowerShell remoting supports the system-wide transcripts and deep
script block logging.
*  We can use winrs in place of PSRemoting to evade the logging (and still reap the benefit of 5985 allowed between hosts):
```
winrs -remote:server1 -u:server1\administrator -p:Pass@1234 hostname
```

• We can also use winrm.vbs and COM objects of WSMan object - https://github.com/bohops/WSMan-WinRM
