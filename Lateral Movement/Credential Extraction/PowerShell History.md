
Another important place to hunt for credentials is Powershell History file, which is usually stored as plain text:

The default location for this file is 
```
Get-Content $env:APPDATA\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt
```
You can get the location by running `Get-PSReadlineOption` and looking at the options. There’s a few history related ones:

- `HistorySavePath` - The file location
- `HistorySaveStyle` - Options are `SaveIncrementally`, which saves after each command; `SaveAtExit`, which appends on exiting PowerShell, or `SaveNothing`, which turns off the history file.
- `MaximumHistoryCount` - The max number of entries to record.

You can modify the options with the `Set-PSReadlineOption` commandlet.

