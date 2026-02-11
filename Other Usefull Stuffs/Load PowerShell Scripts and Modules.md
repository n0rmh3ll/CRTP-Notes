

How we can Run/Load Powershell scripts

*  Load using dot sourcing
```
. C:\AD\Tools\PowerView.ps1
```

* A module (or a script) can be imported with:
```
Import-Module C:\AD\Tools\ADModulemaster\ActiveDirectory\ActiveDirectory.psd1
```

* All the commands in a module can be listed with: 
```
Get-Command -Module <modulename>
```
