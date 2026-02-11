
#amsi

🚨 Usefull and important to be osfuscated and hidden 

We use tools like AMSITrigger and DefenderCheck for Bypassing AV signatures

```
https://github.com/RythmStick/AMSITrigger
```

```
https://github.com/t3hbb/DefenderCheck
```

How to use :
```
AmsiTrigger_x64.exe -i PowerUp.ps1
DefenderCheck.exe PowerUp.ps1
```

*  full obfuscation of PowerShell scripts, we can use Invoke-Obfuscation 
```
https://github.com/danielbohannon/Invoke-Obfuscation
```

* Steps to avoid signature based detection:
1)  Scan using AMSITrigger 
2)  Modify the detected code snippet 
3)  Rescan using AMSITrigger 
4)  Repeat the steps 2 & 3 till we get a result as “AMSI_RESULT_NOT_DETECTED” or “Blank”

