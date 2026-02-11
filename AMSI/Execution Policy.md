Execution Policy is NOT a security measure, it is present to prevent user from `accidently` executing scripts.

* Ways to bypass :
```
powershell -ExecutionPolicy bypass 
powershell -c <cmd>
powershell -encodedcommand 
$env:PSExecutionPolicyPreference="bypass"
```
